# RecuperaSEnha2.php
Criação do recuperasenha2.php

<?php
include('segurancazero.php');
include('conn.php');
if($_SERVER['REQUEST_METHOD'] == 'POST'){
    $codigo = $_POST['cod'];
    $senha = $_POST['senha'];
    $senha2 = $_POST['senha2'];
    $id = $_SESSION['id_recupera'];
    if($senha != $senha2){
        header('Location: recuperasenha2.php?msg=As senhas devem ser iguais');
        exit();
    }
    //verificar se o cod. está correto//
    $sql = "SELECT COUNT(*) FROM tb_usuarios WHERE id_usuario = $id AND
    recupera_usuario = '$codigo'";
    $result = mysqli_query($link,$sql);
    $contador = mysqli_fetch_array($result);

    if($contador[0] == 0){
        //codigo errado//
        header('location: recuperasenha2.php?msg=Codigo Invalido!');
        exit();
    }
    //codigo correto//
    $sql = "UPDATE tb_usuarios SET senha_usuario = '$senha', recupera_usuario = ''
    WHERE id_usuario = $id";
    mysqli_query($link,$sql);
    unset($_SESSION['id_recupera']);
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
    <?php
    include('msg_user.php');
    ?>
    <br><br>
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
