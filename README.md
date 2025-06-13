# RecuperaSenha2.php
Criação do recuperasenha2.php

<?php
// Inclui arquivos de segurança e conexão com o banco de dados //
include('segurancazero.php');
include('conn.php');

// Verifica se o formulário foi enviado via POST //
if($_SERVER['REQUEST_METHOD'] == 'POST'){

    // Captura os dados enviados pelo formulário //
    $codigo = $_POST['cod'];
    $senha = $_POST['senha'];
    $senha2 = $_POST['senha2'];
    $id = $_SESSION['id_recupera']; // ID do usuário armazenado na sessão //

    // Verifica se as senhas digitadas são iguais //
    if($senha != $senha2){
        header('Location: recuperasenha2.php?msg=As senhas devem ser iguais');
        exit();
    }

    // Verifica se o código informado está correto para o usuário //
    $sql = "SELECT COUNT(*) FROM tb_usuarios WHERE id_usuario = $id AND recupera_usuario = '$codigo'";
    $result = mysqli_query($link, $sql);
    $contador = mysqli_fetch_array($result);

    if($contador[0] == 0){
        // Código incorreto //
        header('location: recuperasenha2.php?msg=Codigo Invalido!');
        exit();
    }

    // Busca o tempero (salt) do usuário //
    $sql = "SELECT tempero_usuario FROM tb_usuarios WHERE id_usuario = $id";
    $result = mysqli_query($link, $sql);
    $tbl = mysqli_fetch_array($result);
    $tempero = $tbl[0];

    // Aplica hash MD5 à nova senha com o tempero //
    $senha = md5($senha . $tempero);

    // Aplica hash novamente (duplo MD5, como no cadastro original) //
    $senha = md5($senha);

    // Atualiza a senha no banco e limpa o código de recuperação //
    // Certifique-se de que o ID do usuário está definido na sessão antes de usá-lo //
    $sql = "UPDATE tb_usuarios SET senha_usuario = '$senha', recupera_usuario = '' WHERE id_usuario = $id";
    mysqli_query($link, $sql);

    // Remove o ID da sessão de recuperação //
    unset($_SESSION['id_recupera']);

    // Redireciona para o login com mensagem de sucesso //
    header('Location: login.php?msg=Senha Alterada com Sucesso. Faça seu Login');
    exit();
}
?>

<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Recupera Senha</title>
    <link rel="stylesheet" href="lista.css">
</head>
<body>
    <h1>Recuperar Senha</h1>
    <!-- Exibe mensagens de erro, se houver -->
    <?php
    include('msg_user.php');
    ?>
    <p>Para recuperar sua senha, informe o código que foi enviado para o seu e-mail.</p>
    <p>Se você não recebeu o código, verifique sua caixa de spam ou solicite um novo código.</p>
    <br><br>
    <!-- Formulário para inserir o código e redefinir a senha -->
    <form action="recuperasenha2.php" method="post">
        <label for="cod">Codigo</label>
        <input type="number" name="cod" id="cod" required>
        <br>
        <label for="senha">Senha</label>
        <input type="password" name="senha" id="senha" required>
        <br>
        <label for="senha2">Repita sua senha:</label>
        <input type="password" name="senha2" id="senha2" required>
        <br><br>
        <input type="submit" value="Alterar">
    </form>
</body>
</html>
