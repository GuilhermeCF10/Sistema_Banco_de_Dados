1) Rode 2 queries selecionando nomefunc e nomedepto. Uma com Inner Join e outra com Outer Join

Inner Join: 
SELECT f.Nome as "Funcionário", d.Nome as "Departamento"
from Func f 
INNER JOIN Depto d
ON f.CodDepto = d.CodDepto


Outer Join: 
SELECT f.Nome as "Funcionário", d.Nome as "Departamento"
FROM Func f
LEFT JOIN Depto d
ON f.CodDepto = d.CodDepto