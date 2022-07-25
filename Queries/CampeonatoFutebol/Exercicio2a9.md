2)
SELECT t.NomeTime AS "nome do time", SUM(C.Salario) AS "valorfolha"
FROM contrato c 
INNER JOIN time t ON c.CodTime = t.CodTime
GROUP BY t.NomeTime 


3)
SELECT tg.DescricaoGol AS "tipogol" , AVG (c.Salario) AS "pikadarua"
FROM contrato c
INNER JOIN jogador j ON c.CodJogador = j.CodJog
INNER JOIN gol g ON g.CodJogador = j.CodJog
INNER JOIN tipogol tg on g.CodTipoGol = tg.CodTipoGol
GROUP BY tg.DescricaoGol


4)
SELECT MAX(c.Salario) AS "maiorsalario"
FROM contrato c

5)SELECT t.NomeTime , SUM(c.Salario) as "valorfolha"
FROM contrato c
INNER JOIN time t ON t.CodTime = c.CodTime
GROUP BY t.NomeTime 
HAVING SUM(c.Salario) > 300000
ORDER BY SUM(c.Salario) DESC

6)
SELECT
	t.NomeTime,
    COUNT(*) as "Quantidade Gols"

FROM time t
INNER JOIN gol g on g.CodTime = t.CodTime
INNER JOIN partida p on g.CodPartida = p.CodPartida
WHERE g.EhContra = 0
GROUP BY t.NomeTime



8)SELECT j.NomeJog, COUNT(tg.CodTipoGol) as "gols"
FROM tipogol tg
INNER JOIN gol g on tg.CodTipoGol = g.CodTipoGol
INNER JOIN jogador j on j.CodJog = g.CodJogador
GROUP BY j.NomeJog
HAVING COUNT(tg.CodTipoGol) > 2

SELECT j.NomeJog, COUNT(tg.CodTipoGol) as "gols"
FROM tipogol tg
INNER JOIN gol g on tg.CodTipoGol = g.CodTipoGol
INNER JOIN jogador j on j.CodJog = g.CodJogador
WHERE g.EhContra = False and tg.DescricaoGol = "cabe�a"
GROUP BY j.NomeJog
HAVING COUNT(tg.CodTipoGol) > 2


9)
SELECT p.DataPartida, e.NomeEstadio, COUNT(g.CodGol)
FROM gol g
INNER JOIN partida p ON g.CodPartida = p.CodPartida
INNER JOIN estadio e ON p.CodEstadio = e.CodEstadio
GROUP BY p.DataPartida