CREATE TABLE IF NOT EXISTS jogador (
    CodJogador BIGINT AUTO_INCREMENT NOT NULL,
    Nome VARCHAR(255),
    DataNascimento DATE,
    PRIMARY KEY (CodJogador)
	);

CREATE TABLE IF NOT EXISTS `time` (
    CodTime BIGINT AUTO_INCREMENT NOT NULL,
    Nome VARCHAR(255),
    DataFundacao DATE,
    PRIMARY KEY (CodTime)
	);
    
CREATE TABLE IF NOT EXISTS estadio (
    CodEstadio BIGINT AUTO_INCREMENT NOT NULL,
    Nome VARCHAR(255),
    DataFundacao DATE,
    PRIMARY KEY (CodEstadio)
	);
    
CREATE TABLE IF NOT EXISTS contrato (
    CodContrato BIGINT AUTO_INCREMENT NOT NULL,
    DataInicio DATE,
    DataTermino DATE,
    PRIMARY KEY (CodContrato),
    CodJogador_idx BIGINT,
    INDEX CodJogador_idx (CodJogador),
    FOREIGN KEY (CodJogador) 
    REFERENCES jogador (CodJogador)
    ON UPDATE CASCADE
    ON DELETE RESTRICT,
    CodTime_idx BIGINT,
    INDEX CodTime_idx (CodTime),
    FOREIGN KEY (CodTime) 
    REFERENCES `time` (CodTime)
    ON UPDATE CASCADE
    ON DELETE RESTRICT
	);





CREATE TABLE IF NOT EXISTS jogo (
    CodJogo BIGINT AUTO_INCREMENT NOT NULL,
    Nome VARCHAR(255),
    DataHoraJogo TIMESTAMP,
    PRIMARY KEY (CodJogo),
    CodMandante_idx BIGINT,
    INDEX CodMandante_idx (CodTime),
    FOREIGN KEY (CodTime) 
    REFERENCES `time` (CodTime)
    ON UPDATE CASCADE
    ON DELETE RESTRICT,
    CodVisitante_idx BIGINT,
    INDEX CodVisitante_idx (CodTime),
    FOREIGN KEY (CodTime) 
    REFERENCES `time` (CodTime)
    ON UPDATE CASCADE
    ON DELETE RESTRICT,
    CodEstadio_idx BIGINT,
    INDEX CodEstadio_idx (CodEstadio),
    FOREIGN KEY (CodEstadio) 
    REFERENCES estadio (CodEstadio)
    ON UPDATE CASCADE
    ON DELETE RESTRICT
	);





CREATE TABLE IF NOT EXISTS tipogol (
    CodTipoGol BIGINT AUTO_INCREMENT NOT NULL,
    Nome VARCHAR(255),
    PRIMARY KEY (CodTipoGol)
	);

CREATE TABLE IF NOT EXISTS gol (
    CodGol BIGINT AUTO_INCREMENT NOT NULL,
    DescriDetalhada VARCHAR(255),
    Tempo INT,
    EhContra boolean,
    Minutos INT,
    PRIMARY KEY (CodGol),
    CodJogadorFez_idx BIGINT,
    INDEX CodJogadorFez_idx (CodJogador),
    FOREIGN KEY (CodJogador) 
    REFERENCES `jogador` (CodJogador)
    ON UPDATE CASCADE
    ON DELETE RESTRICT,
    CodJogadorAssistiu_idx BIGINT,
    INDEX CodJogadorAssistiu_idx (CodJogador),
    FOREIGN KEY (CodJogador) 
    REFERENCES `jogador` (CodJogador)
    ON UPDATE CASCADE
    ON DELETE RESTRICT,
    CodJogo_idx BIGINT,
    INDEX CodJogo_idx (CodJogo),
    FOREIGN KEY (CodJogo) 
    REFERENCES jogo (CodJogo)
    ON UPDATE CASCADE
    ON DELETE RESTRICT,
    CodTipoGol_idx BIGINT,
    INDEX CodTipoGol_idx (CodTipoGol),
    FOREIGN KEY (CodTipoGol) 
    REFERENCES tipogol (CodTipoGol)
    ON UPDATE CASCADE
    ON DELETE RESTRICT
	);

INNER JOIN time AS m (m.CodTime = j.CodMandante_idx)



AULA 18/07/2022

Qual a folha de pagamento atual de cada time?
SELECT 
		t.Nome AS nomeTime,
        sum(c.salario) AS valorFolha
FROM time AS t
INNER JOIN contrato AS c ON (t.CodTime = c.CodTime_idx)
WHERE extract(YEAR_MONTH FROM c.DataInicio) = '202203'
GROUP BY t.Nome

Qual o salario medio dos jogadores para cada tipo de gol feito?

SELECT 
	tg.Nome AS tipoGol,
	AVG(c.salario) AS salarioMedio
FROM gol AS g
INNER JOIN tipogol AS tg ON (tg.CodTipoGol = g.CodTipoGol_idx)
INNER JOIN contrato AS c ON (g.CodJogadorFez_idx = c.CodJogador_idx)
GROUP BY tg.Nome

Qual � o valor do maior salario pago no campeonato?

SELECT 
	max(salario) AS maiorSalario 
FROM contrato

Liste os nomes do time e sua folha de pagamento apenas 
para os time com uma folha maior que 300 mil reais
SELECT 
		t.Nome AS nomeTime,
        sum(c.salario) AS valorFolha
FROM time AS t
INNER JOIN contrato AS c ON (t.CodTime = c.CodTime_idx)
WHERE extract(YEAR_MONTH FROM c.DataInicio) = '202203'
GROUP BY t.Nome
HAVING sum(c.salario)>60000
ORDER BY 2 DESC

Liste o total de gols, que nao sejam contra,
feitos por cada time no primeiro trismestre de 
2022

SELECT 
	t.Nome AS nomeTime,
	count(g.CodGol) AS totalGols
FROM jogo AS j
INNER JOIN gol AS g ON (j.CodJogo = g.CodJogo_idx)
INNER JOIN contrato AS c ON (g.CodJogadorFez_idx = c.CodJogador_idx)
INNER JOIN time AS t ON (t.CodTime = c.CodTime_idx)
WHERE QUARTER(j.DataHoraJogo) = 1 AND g.EhContra IS FALSE
GROUP BY t.Nome


Liste o total de gols, que nao foram contra, por time e tipo 
no ano de 2022 

SELECT 
	t.Nome AS nomeTime,
	tg.Nome AS tipoGol,
    count(g.CodGol) AS totalGols
FROM jogo AS j
INNER JOIN gol AS g ON (j.CodJogo = g.CodJogo_idx)
INNER JOIN tipogol AS tg ON (tg.CodTipoGol = g.CodTipoGol_idx)
INNER JOIN contrato AS c ON (g.CodJogadorFez_idx = c.CodJogador_idx)
INNER JOIN time AS t ON (t.CodTime = c.CodTime_idx)
WHERE g.EhContra IS FALSE
GROUP BY t.Nome, tg.Nome

Liste o total de gols de cabe�a feitos por cada 
jogador apenas para os jogadores que fizeram mais de dois gols
de cabe�a


SELECT 
	t.Nome AS nomeJogador,
	tg.Nome AS tipoGol,
    	count(g.CodGol) AS totalGols
FROM jogo AS j
INNER JOIN gol AS g ON (j.CodJogo = g.CodJogo_idx)
INNER JOIN tipogol AS tg ON (tg.CodTipoGol = g.CodTipoGol_idx)
INNER JOIN contrato AS c ON (g.CodJogadorFez_idx = c.CodJogador_idx)
INNER JOIN jogador AS t ON (t.CodJogador = c.CodJogador_idx)
WHERE g.EhContra IS FALSE 
AND tg.Nome = 'Cabe�a'
GROUP BY t.Nome, tg.Nome
HAVING count(g.CodGol) > 2


Liste a data da partida, o estadio e o total de gols feitos na partida 

SELECT 
	j.DataHoraJogo AS data_jogo,
	e.Nome AS nomeEstadio,
	count(*) AS totalGols
FROM jogo AS j
INNER JOIN gol AS g ON (j.CodJogo = g.CodJogo_idx)
INNER JOIN estadio AS e ON (j.CodEstadio_idx = e.CodEstadio)
GROUP BY j.DataHoraJogo, e.Nome



