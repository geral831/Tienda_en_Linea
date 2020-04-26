# Tienda_en_Linea

### Caso de uso 12: Inicio de sesión del Administrador

![](https://github.com/geral831/Tienda_en_Linea/blob/master/Docs/cu12.png)

### Código PHP
```php
		
<?php
//Inicializa la sesion 
session_start();
//Verifica si el usuario ha iniciado sesion 
if(isset($_SESSION["loggedin"]) && $_SESSION["loggedin"] === true ){
    header("location:");
    exit;

}
//Hace referencia al archivo de configuracion
require_once "config.php";
//Define e inicializa las variables
$nameAdmin = $contrasenia = "";
$nameAdmin_err = $contrasenia_err = "";

if($_SERVER["REQUEST_METHOD"] == "POST"){
    
    //Verifica si el nombre del Administrador es vacio
    if(empty(trim($_POST["nameAdmin"]))){
        $nameAdmin_err = "Ingresa tu nombre de Administrador";

    }
    else{
        $nameAdmin = trim($_POST["nameAdmin"]);
    }

    //Verifica si la contraseña es vacio
    if(empty(trim($_POST["contrasenia"]))){
        $contrasenia_err = "Ingresa tu contraseña"; 
    }
    else{
        $contrasenia = trim($_POST["contrasenia"]);
    }

    //VALIDA LAS CREDENCIALES
    if(empty($nameAdmin_err) && empty($contrasenia_err)){

        //prepara la consulta
        $sql = "SELECT id, nameAdmin, contrasenia FROM usuario_administrador WHERE nameAdmin =?";

        if($stmt = $mysqli->prepare($sql)){
            //Declara los parametros
            $stmt ->bind_param("s", $param_nameAdmin);
            
            $param_nameAdmin = $nameAdmin;

            //Ejecuta la declaracion preparada
            if($stmt->execute()){

                $stmt->store_result();
                
                //Verifica si existe el usuario y si la contraseña es correcta
                if($stmt->num_rows ==1){
                    $stmt->bind_result($id, $nameAdmin, $hashed_password);
    
                    if($stmt->fetch()){
                        if(password_verify($contrasenia, $hashed_password)){
                            //Si la contraseña es correcta se inicia la sesion
                            session_start();
                            
                            //Almacena las variables de sesion
                            $_SESSION["loggedin"] = true;
                            $_SESSION["id"] = $id;
                            $_SESSION["nameAdmin"] = $nameAdmin;
                            
                            //Redirecciona a la pagina de inicio
                            header("location: LoginAdmin.php");
                        } else{
                            //Mensaje de error: La contraseña es invalida
                            $contrasenia_err = "La contraseña es incorrecta... Intentalo de nuevo";
                        }
    
                    }
                } else{
                    //Mensaje de error: El usurio no existe
                    $nameAdmin_err = "No se encontró una cuenta de Administrador con ese nombre";
                }
    
            } else{
                echo "Algo salio mal...Intentalo de nuevo";
            }   
            //Cierra el statement 
            $stmt->close();
        }
    }

    //Cierra la conexion
    $mysqli-> close();

}
?>

		
```

### Código HTML

```html

<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>InicioSesiónAdministrador</title>
    <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.css">
    <style type="text/css">
        body{ font: 14px sans-serif; }
        .wrapper{ width: 350px; padding: 40px; margin-left:35%; margin-top:30pt}
        body{
        background-repeat: no-repeat;
        background-size: cover; 
        
        background-attachment: fixed;
        min-height: 90%;
      }
    </style>


</head>
<body>

<nav class="navbar navbar-inverse">
        <div class="container-fluid">
            <div class="navbar-header">
                <a class="navbar-brand" href="#">Online Store</a>
            </div>
            <ul class="nav navbar-nav navbar-right">
                <li><a href="#"><span class="glyphicon glyphicon-user"></span> Sign Up</a></li>
                <li><a href="#"><span class="glyphicon glyphicon-log-in"></span> Login</a></li>
            </ul>
        </div>
    </nav>    

<div class="wrapper">
        <h2>Iniciar Sesión como Administrador</h2>
        <p>Ingresa tus datos.</p>
        <form action="<?php echo htmlspecialchars($_SERVER["PHP_SELF"]); ?>" method="post">
            <div class="form-group <?php echo (!empty($nameAdmin_err)) ? 'has-error' : ''; ?>">
                <label>Nombre Adminstrador</label>
                <input type="text" name="nameAdmin" class="form-control" value="<?php echo $nameAdmin; ?>"> 
                <span class="help-block"><?php echo $nameAdmin_err; ?></span>
            </div>    
            <div class="form-group <?php echo (!empty($contrasenia_err)) ? 'has-error' : ''; ?>">
                <label>Contraseña</label>
                <input type="password" name="contrasenia" class="form-control">
                <span class="help-block"><?php echo $contrasenia_err; ?></span>
            </div>
            <div class="form-group">
                <input type="submit" class="btn btn-primary" value="Iniciar Sesión">
            </div>
            <p>¿No tienes una cuenta? <a href="#"> Crear una cuenta</a>.</p>
        </form>
    </div>    


    
</body>
</html>
```
