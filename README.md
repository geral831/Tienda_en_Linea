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

