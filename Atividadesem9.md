Para aplicar recursos de controle de acesso e permissão em um banco de dados relacionado ao tema de treino de academia, podemos criar objetos como functions, procedures, triggers e events que restrinjam o acesso a determinados dados ou operações. Seguem abaixo alguns exemplos de objetos que podemos criar:

1. Function: podemos criar uma function que retorne apenas os dados de treinos de um usuário específico, com base em seu ID. A function poderia ser definida da seguinte forma:

```
CREATE FUNCTION listar_treinos_usuario(@usuario_id INT)
RETURNS TABLE
AS
RETURN
  SELECT * FROM Treino WHERE usuario_id = @usuario_id;
```

2. Procedure: podemos criar uma procedure que permita que apenas usuários com permissão de administrador adicionem novos exercícios ao banco de dados. A procedure poderia ser definida da seguinte forma:

```
CREATE PROCEDURE adicionar_exercicio
  @nome VARCHAR(255),
  @descricao TEXT,
  @admin_only BIT = 0
AS
BEGIN
  IF (@admin_only = 1 AND USER_NAME() <> 'admin')
  BEGIN
    RAISERROR('Apenas administradores podem adicionar novos exercícios.', 16, 1);
    RETURN;
  END
  INSERT INTO Exercicio (nome, descricao) VALUES (@nome, @descricao);
END;
```

3. Trigger: podemos criar um trigger que restrinja a exclusão de usuários com treinos associados a eles. O trigger poderia ser definido da seguinte forma:

```
CREATE TRIGGER restricao_exclusao_usuario
ON Usuario
FOR DELETE
AS
BEGIN
  IF EXISTS (SELECT 1 FROM Treino WHERE usuario_id IN (SELECT deleted.id FROM deleted))
  BEGIN
    RAISERROR('Não é possível excluir um usuário com treinos associados.', 16, 1);
    ROLLBACK TRANSACTION;
    RETURN;
  END
END;
```

4. Event: podemos criar um event que envie um e-mail para o administrador sempre que um novo usuário for registrado no sistema. O event poderia ser definido da seguinte forma:

```
CREATE EVENT enviar_email_novo_usuario
ON SCHEDULE AFTER INSERT
ON Usuario
DO
BEGIN
  DECLARE @usuario_id INT;
  SET @usuario_id = (SELECT id FROM inserted);
  DECLARE @usuario_email VARCHAR(255);
  SET @usuario_email = (SELECT email FROM inserted);
  DECLARE @admin_email VARCHAR(255);
  SET @admin_email = 'admin@academia.com';
  -- Lógica para enviar e-mail de notificação para o administrador
END;
```

Esses são apenas alguns exemplos de como podemos aplicar recursos de controle de acesso e permissão em um banco de dados relacionado ao tema de treino de academia. Cada caso de uso pode exigir diferentes abordagens e soluções, dependendo das necessidades específicas do projeto.