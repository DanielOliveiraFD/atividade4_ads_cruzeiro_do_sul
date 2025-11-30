# Atividade 4 - ADS Cruzeiro do Sul
## Experiência Prática IV | Implementação e Manipulação de Dados

---

## 1. Schema do Banco de Dados
**Instrução:** Execute este bloco primeiro para criar a estrutura das tabelas e inserir os dados iniciais.

```sql
-- 1. Tabela de Usuários
CREATE TABLE usuarios (
    id_usuario INT AUTO_INCREMENT PRIMARY KEY,
    nome VARCHAR(100) NOT NULL,
    cpf VARCHAR(14) UNIQUE NOT NULL,
    endereco VARCHAR(200),
    telefone VARCHAR(20),
    data_nascimento DATE
);

-- 2. Tabela de Livros
CREATE TABLE livros (
    id_livro INT AUTO_INCREMENT PRIMARY KEY,
    titulo VARCHAR(150) NOT NULL,
    autor VARCHAR(100) NOT NULL,
    editora VARCHAR(50),
    genero VARCHAR(50),
    isbn VARCHAR(20) UNIQUE,
    quantidade_estoque INT DEFAULT 1 -- 
);

-- 3. Tabela de Empréstimos (A tabela que liga usuários aos livros)
CREATE TABLE emprestimos (
    id_emprestimo INT AUTO_INCREMENT PRIMARY KEY,
    id_usuario INT,
    id_livro INT,
    data_emprestimo DATE,
    data_prevista_devolucao DATE, -- Ex: 26/12/2025
    data_real_devolucao DATE NULL, -- Fica NULL até a pessoa devolver
    status_emprestimo VARCHAR(20) DEFAULT 'ATIVO', -- ATIVO ou DEVOLVIDO
    FOREIGN KEY (id_usuario) REFERENCES usuarios(id_usuario),
    FOREIGN KEY (id_livro) REFERENCES livros(id_livro)
);

-- Inserindo Usuários
INSERT INTO usuarios (nome, cpf, endereco, telefone, data_nascimento) VALUES 
('Daniel', '123.456.789-00', 'Rua das Flores, 10', '(21) 99999-0000', '1990-05-15'),
('Geovana', '111.222.333-44', 'Av. Central, 500', '(21) 98888-1111', '1985-10-20'),
('Carlos Eduardo', '555.666.777-88', 'Praça Santana, 44', '(24) 97777-2222', '2003-01-30');

-- Inserindo Livros
INSERT INTO livros (titulo, autor, editora, genero, isbn, quantidade_estoque) VALUES 
('O Código da Vinci', 'Dan Brown', 'Arqueiro', 'Suspense', '978-8575420272', 3),
('Dom Casmurro', 'Machado de Assis', 'Globo', 'Romance', '978-8525046552', 5),
('Harry Potter e a Pedra Filosofal', 'J.K. Rowling', 'Rocco', 'Fantasia', '978-8532511010', 2),
('Clean Code', 'Robert C. Martin', 'Alta Books', 'Tecnologia', '978-8576082675', 1);

-- Inserindo Empréstimos (simulando o cenario)
-- Daniel (ID 1) pegou Código da Vinci (ID 1)
INSERT INTO emprestimos (id_usuario, id_livro, data_emprestimo, data_prevista_devolucao, status_emprestimo) 
VALUES (1, 1, '2025-12-10', '2025-12-26', 'ATIVO');

-- Geovana (ID 2) pegou Dom Casmurro (ID 2)
INSERT INTO emprestimos (id_usuario, id_livro, data_emprestimo, data_prevista_devolucao, status_emprestimo) 
VALUES (2, 2, '2025-11-01', '2025-11-15', 'ATIVO');

-- 1. Listar todos os livros emprestados no momento, mostrando nome do usuário e título do livro (USO DE JOIN e WHERE)
SELECT 
    u.nome AS Nome_Usuario, 
    l.titulo AS Livro_Emprestado, 
    e.data_prevista_devolucao
FROM emprestimos e
JOIN usuarios u ON e.id_usuario = u.id_usuario
JOIN livros l ON e.id_livro = l.id_livro
WHERE e.status_emprestimo = 'ATIVO';

-- 2. Listar os livros com pouco estoque (menos de 3 unidades),(USO DE WHERE e ORDER BY)
SELECT titulo, quantidade_estoque 
FROM livros 
WHERE quantidade_estoque < 3 
ORDER BY titulo ASC;

-- 3. Consultar histórico de empréstimos do usuário
SELECT l.titulo, e.data_emprestimo, e.status_emprestimo
FROM emprestimos e
JOIN usuarios u ON e.id_usuario = u.id_usuario
JOIN livros l ON e.id_livro = l.id_livro
WHERE u.nome LIKE '%Daniel%';

-- --- COMANDOS DE UPDATE ---

-- 1. Simular a DEVOLUÇÃO de um livro
-- Atualiza o status para DEVOLVIDO e coloca a data de hoje na devolução real
UPDATE emprestimos 
SET status_emprestimo = 'DEVOLVIDO', data_real_devolucao = CURRENT_DATE 
WHERE id_emprestimo = 2; -- Supondo que seja a devolução da Geovana

-- 2. Atualizar o endereço de um usuário que mudou de casa
UPDATE usuarios 
SET endereco = 'Rua Nova, 100 - Centro' 
WHERE id_usuario = 1;

-- 3. Corrigir a quantidade de estoque de um livro (chegaram mais exemplares)
UPDATE livros 
SET quantidade_estoque = 10 
WHERE titulo = 'Dom Casmurro';


-- --- COMANDOS DE DELETE ---

-- 1. Remover um livro que foi perdido ou destruído e não existe mais no acervo
DELETE FROM livros 
WHERE isbn = '978-8576082675'; -- Deletando o Clean Code

-- 2. Remover um usuário que pediu para sair do sistema (Somente se não tiver empréstimos ativos!)
DELETE FROM usuarios 
WHERE id_usuario = 3; -- Deletando Carlos Eduardo

-- 3. Cancelar um registro de empréstimo feito por engano (Ex: bibliotecário digitou errado)
DELETE FROM emprestimos 
WHERE id_emprestimo = 5; -- Supondo um ID que foi criado errado´´´
