this file is called index.php
<?php
 
session_start();
 
if(isset($_GET['logout'])){    
     
    //Simple exit message
    $logout_message = "<div class='msgln'><span class='left-info'>User <b class='user-name-left'>". $_SESSION['name'] ."</b> has left the chat session.</span><br></div>";
    file_put_contents("log.html", $logout_message, FILE_APPEND | LOCK_EX);
     
    session_destroy();
    header("Location: index.php"); //Redirect the user
}
 
if(isset($_POST['enter'])){
    if($_POST['name'] != ""){
        $_SESSION['name'] = stripslashes(htmlspecialchars($_POST['name']));
    }
    else{
        echo '<span class="error">Please type in a name</span>';
    }
}
 
function loginForm(){
    echo
    '<div id="loginform">
    <h1>Kool Chat</h1>
    <p>Please enter your name to continue!</p>
    <form action="index.php" method="post">
      <label for="name">Name &mdash;</label>
      <input type="text" name="name" id="name" />
      <input type="submit" name="enter" id="enter" value="Enter" />
    </form>
  </div>';
}
 
?>
 
<!DOCTYPE html>
<html lang="pt">
    <head>
        <meta charset="utf-8" />
 
        <title>Kool Chat</title>
        <meta name="description" content="Kool Chat" />
        <link rel="stylesheet" href="style.css" />
    </head>
    <body>
    <?php
    if(!isset($_SESSION['name'])){
        loginForm();
    }
    else {
    ?>
        <div id="wrapper">
            <div id="menu">
                <p class="welcome">Welcome, <b><?php echo $_SESSION['name']; ?></b></p>
                <p class="logout"><a id="exit" href="#">leave chat</a></p>
            </div>
 
            <div id="chatbox">
            <?php
            if(file_exists("log.html") && filesize("log.html") > 0){
                $contents = file_get_contents("log.html");          
                echo $contents;
            }
            ?>
            </div>
 
            <form name="message" action="">
                <input name="usermsg" type="text" id="usermsg" />
                <input name="submitmsg" type="submit" id="submitmsg" value="Send" />
            </form>
        </div>
        <script type="text/javascript" src="https://cdnjs.cloudflare.com/ajax/libs/jquery/3.5.1/jquery.min.js"></script>
        <script type="text/javascript">
            // jQuery Document
            $(document).ready(function () {
                $("#submitmsg").click(function () {
                    var clientmsg = $("#usermsg").val();
                    $.post("post.php", { text: clientmsg });
                    $("#usermsg").val("");
                    return false;
                });
 
                function loadLog() {
                    var oldscrollHeight = $("#chatbox")[0].scrollHeight - 20; //Scroll height before the request
 
                    $.ajax({
                        url: "log.html",
                        cache: false,
                        success: function (html) {
                            $("#chatbox").html(html); //Insert chat log into the #chatbox div
 
                            //Auto-scroll           
                            var newscrollHeight = $("#chatbox")[0].scrollHeight - 20; //Scroll height after the request
                            if(newscrollHeight > oldscrollHeight){
                                $("#chatbox").animate({ scrollTop: newscrollHeight }, 'normal'); //Autoscroll to bottom of div
                            }   
                        }
                    });
                }
 
                setInterval (loadLog, 2500);
 
                $("#exit").click(function () {
                    var exit = confirm("Are you sure you want to end the session?");
                    if (exit == true) {
                    window.location = "index.php?logout=true";
                    }
                });
            });
        </script>
    </body>
</html>
<?php
}
?>

this file is called log.html
<div class='msgln'><span class='chat-time'>3:34 PM</span> <b class='user-name'>AgentCube</b> hi<br></div><div class='msgln'><span class='chat-time'>3:34 PM</span> <b class='user-name'>youlikemen699999</b> hi<br></div><div class='msgln'><span class='left-info'>User <b class='user-name-left'>AgentCube</b> has left the chat session.</span><br></div>

this file is called post.php
<?php
session_start();
if(isset($_SESSION['name'])){
    $text = $_POST['text'];
     
    $text_message = "<div class='msgln'><span class='chat-time'>".date("g:i A")."</span> <b class='user-name'>".$_SESSION['name']."</b> ".stripslashes(htmlspecialchars($text))."<br></div>";
    file_put_contents("log.html", $text_message, FILE_APPEND | LOCK_EX);
}
?>

this file is called style.css
* {
    margin: 0;
    padding: 0;
}

body {
    margin: 20px auto;
    font-family: "Lato";
    font-weight: 300;
    background-color: #333; /* Dark background color */
    color: #fff; /* Light text color */
}

form {
    padding: 15px 25px;
    display: flex;
    gap: 10px;
    justify-content: center;
}

form label {
    font-size: 1.5rem;
    font-weight: bold;
    color: #fff; /* Light text color */
}

input {
    font-family: "Lato";
    background-color: #444; /* Slightly darker input background */
    color: #fff; /* Light text color */
}

a {
    color: #00f;
    text-decoration: none;
}

a:hover {
    text-decoration: underline;
}

#wrapper,
#loginform {
    margin: 0 auto;
    padding-bottom: 25px;
    background: #444; /* Darker background color */
    width: 600px;
    max-width: 100%;
    border: 2px solid #000; /* Black border */
    border-radius: 4px;
}

#loginform {
    padding-top: 18px;
    text-align: center;
}

#loginform p {
    padding: 15px 25px;
    font-size: 1.4rem;
    font-weight: bold;
    color: #fff; /* Light text color */
}

#chatbox {
    text-align: left;
    margin: 0 auto;
    margin-bottom: 25px;
    padding: 10px;
    background: #222; /* Darker chatbox background */
    height: 300px;
    width: 530px;
    border: 1px solid #000; /* Black border */
    overflow: auto;
    border-radius: 4px;
    border-bottom: 4px solid #000; /* Black border */
    color: #fff; /* Light text color */
}

#usermsg {
    flex: 1;
    border-radius: 4px;
    border: 1px solid #ff9800;
    background-color: #444; /* Slightly darker input background */
    color: #fff; /* Light text color */
}

#name {
    border-radius: 4px;
    border: 1px solid #ff9800;
    padding: 2px 8px;
    background-color: #444; /* Slightly darker input background */
    color: #fff; /* Light text color */
}

#submitmsg,
#enter {
    background: #ff9800;
    border: 2px solid #e65100;
    color: white;
    padding: 4px 10px;
    font-weight: bold;
    border-radius: 4px;
}

.error {
    color: #ff0000;
}

#menu {
    padding: 15px 25px;
    display: flex;
}

#menu p.welcome {
    flex: 1;
}

a#exit {
    color: white;
    background: #c62828;
    padding: 4px 8px;
    border-radius: 4px;
    font-weight: bold;
}

.msgln {
    margin: 0 0 5px 0;
}

.msgln span.left-info {
    color: orangered;
}

.msgln span.chat-time {
    color: #666;
    font-size: 60%;
    vertical-align: super;
}

.msgln b.user-name,
.msgln b.user-name-left {
    font-weight: bold;
    background: #546e7a;
    color: white;
    padding: 2px 4px;
    font-size: 90%;
    border-radius: 4px;
    margin: 0 5px 0 0;
}

.msgln b.user-name-left {
    background: orangered;
}

that is all the files of my website I want you to modify the website so the ui is bigger and more friendly and more rounded and has simple animations like when you hover over stuff it gets bigger and also change the color scheme from black and orange to like black and grey and white
