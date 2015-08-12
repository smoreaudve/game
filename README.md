Inscription --------------------------------------------

<!doctype html>
<html lang="fr">
<head>
  <meta charset="utf-8">
  <title>Inscription</title>
</head>
<body>
    <?php 
    session_start();
    if (isset($_SESSION['id']) AND isset($_SESSION['pseudo']))
    {
        echo 'Bonjour ' . $_SESSION['pseudo'];
    }
    else
    {      
        try { $bdd = new PDO('mysql:host=localhost;dbname=test;charset=utf8', 'root', ''); }
            catch(Exception $e) { die('Erreur : '.$e->getMessage()); }
             
    	 $verif_pass = '';
    	 $verif_mail = '';
    	 $verif_pseudo= '';
         if(isset($_POST['valider']))
         {
         
            $pass_hache = sha1($_POST['pass']);
            $pseudo = htmlspecialchars($_POST['pseudo']);
            $email = htmlspecialchars($_POST['email']);
     
            // PARTIE 1 : Tester si le pseudo existe et est correct -------------------------------------------  
            if ($_POST['pseudo']){
     
                $reponse = $bdd->query("SELECT pseudo FROM users WHERE pseudo='".$pseudo."'");
                $donnees = $reponse->fetch();
                if (empty($donnees['pseudo']))
                {
                    $strings = array(($_POST['pseudo']));
                    foreach ($strings as $testcase) {
                      if (ctype_alnum($testcase)) 
                      {
                        echo "La chaine $testcase contient des chiffres ou des lettres.\n <br />";
                        $verif_pseudo = 1;
                      } 
                      else 
                      {
                        echo "La chaine $testcase ne contient pas que des chiffres ou des lettres.\n <br />";
                      }
                    }
                }
                else
                {
                    echo 'le pseudo existe deja en bdd <br />';
                }
            }
            else
            {
                echo 'il faut ecrire un pseudo <br />';
            }      
            // End Partie 1 ---------------------------------------
     
     
            // Partie 2:  Tester si le mail existe déjà et s'il est correct  -------------------------------------------  
             $_POST['email'] = htmlspecialchars($_POST['email']); // On rend inoffensives les balises HTML que le visiteur a pu rentrer
            if (preg_match("#^[a-z0-9._-]+@[a-z0-9._-]{2,}\.[a-z]{2,4}$#", $_POST['email']))
            {
                $reponse = $bdd->query("SELECT email FROM users WHERE email='".$email."'");
                $donnees = $reponse->fetch();
                if (empty($donnees['email']))
                {
                    echo 'il y a pas le mail <br />';
                    $verif_mail = 1;
                }
                else
                {
                echo 'le mail existe deja en bdd <br />';
                }
            }
            else
            {
                echo 'L\'adresse  n\'est pas valide, recommencez !';
            }
            // End Partie 2 ---------------------------------------
     
     
            // Partie 3 : Tester si les 2 mots de passes ----------
            if ($_POST['pass'] && $_POST['pass2']){
                if ($_POST['pass'] == $_POST['pass2']) {
                    echo ' passe pareil';
                    $verif_pass = 1;
                }
                else
                {
                    echo 'pass différent';
                }
            }
            else {
                 echo 'il faut un mot de passe';
            }
            // End Partie 3 ---------------------------------------
         }
          
      if ($verif_pass && $verif_mail && $verif_pseudo)
      {
      	echo 'on peut lancer lexecution';
      	 $req = $bdd->prepare('INSERT INTO users(pseudo, pass, email, date_inscription) VALUES(:pseudo, :pass, :email, CURDATE())');
            $req->execute(array(
                'pseudo' => $pseudo,
                'pass' => $pass_hache,
                'email' => $email));
      }
      ?>
        <form action='' method='post'>
            * Pseudo :<input type='texte' name='pseudo'> <br />
            * Mot de passe :<input type='texte' name='pass'> <br />
            * Verifier Mot de passe : <input type='texte' name='pass2'> <br />
            * Email :<input type='texte' name='email'> <br />
            <input type="submit" name="valider">
        </form>
        <a href="login.php">se loguer</a>
      <?php
    }
?>
<p>Deja inscrit et logue</p>
<a href="test.php">Acces partie</a>
<a href="login.php">Acces deconnexion</a>

</body>
</html>

Test --------------------------------------------

<!doctype html>
<html lang="fr">
<head>
  <meta charset="utf-8">
  <title>Game</title>
</head>
<body>

<?php 
	 /* Récupère utilisateurs si connecté */
	 	session_start();
		if (isset($_SESSION['id']) AND isset($_SESSION['pseudo']))
		{
		    echo 'Bonjour ' . $_SESSION['pseudo']; ?> <a href="login.php">Acces déconnexion</a><?php
		}	
	/* End Récupère utilisateurs si connecté */
?>

<!-- Fomulaire de recherche -->
	<p>Chercher un element :</p>
	<form action="test.php" method="get">
		<p>
		    <input type="text" name="ville" />
		    <input type="submit" value="Valider" />
		</p>
	</form>
<!-- End Fomulaire de recherche -->


<?php

	if (isset($_GET['bouton'])) {
	    $btn = $_GET['bouton'];
	}
	/* End Récupère utilisateurs si connecté */
?>


<?php 
/* Si une ville est présente dans l'URL */
	if (isset($_GET['ville'])) {
		$id = $_GET['ville'];
		try { $bdd = new PDO('mysql:host=localhost;dbname=test;charset=utf8', 'root', ''); }
		catch(Exception $e) { die('Erreur : '.$e->getMessage()); }

		/* Récupère situation actuelle de la ville, si elle existe */
			$reponse = $bdd->query('SELECT * FROM ville WHERE nom="'.$id.'"');
			$donnees = $reponse->fetch();		
			if($donnees == false)
			     {
			    $erreur="La ville n'existe pas. Essayer une existante";
			    echo $erreur;
			    return;
		     }
		    $hab = $donnees['hab'];
			$id_ville = $donnees['id'];
			$poli = $donnees['poli'];
			$poll = $donnees['poll'];
	     /* Récupère situation actuelle de la ville */
		

		/* Affichage statistiques actuel de la ville */	
			if (isset($habInc)) {
				echo $id. ' possede number hab : ' .$habInc; 
				}
			else if (isset($hab)) {
				echo $id. ' possede number hab : ' .$hab; 
				}

			echo '<br />id ville: ' .$id_ville;
			echo '<br />police: ' .$poli;
			echo '<br />pollution: ' .$poli;
			echo '<br />id utilisateur: ' .$_SESSION['id'];
		/* Affichage statistiques actuel de la ville */	


		/* Récupère des date */
			$reponse_inclusion = $bdd->query('SELECT * FROM inclusion WHERE (id_users="'.$_SESSION['id'].'") AND (id_ville="'.$id_ville.'" )');
			$donnees_inclusion = $reponse_inclusion->fetch();	
		
			$last_click_hour = $donnees_inclusion['click_hour'];  /*=>*/	$d2 = new DateTime($last_click_hour);
			$date = date('Y-m-d H:i:s'); 						  /*=>*/	$d1 = new DateTime($date);



			$TempsRestant = $d2->diff($d1);
			printf("<br /> Dernier vote il y a %s jour %s heures %s minutes %s secondes",
			$TempsRestant->d, $TempsRestant->h,$TempsRestant->i, $TempsRestant->s);
			
			$temps_construction = new DateInterval('PT10M');
		/* End Récupère des date */


		/* Gestion mise à jour des dates et autorisation du click */
			if($d1->sub($temps_construction) > $d2) {     
			    echo '<br />Bâtiment construit !';
			    $finish = true;	 

				$req = $bdd->prepare('UPDATE inclusion SET click_hour = :click_hours WHERE id_users= :mysess AND id_ville= :idvillage');
				$req->execute(array(
					'click_hours' => $date,
					'mysess' => $_SESSION['id'],
					'idvillage' => $id_ville
					));


				if (isset($_SESSION['id'])) { 
					?>
					<form action="test.php" method="get">
					<p>
						<input type="hidden" name="ville" value="<?php echo $id ?>">
					    <input type="hidden" name="bouton" value="1">
					    <input type="submit" value="ajouter hab" />
					</p>
					</form>
					<?php									

					if (isset($btn)) {
						$habInc= ++$hab; 
						$nb_modifs = $bdd->exec('UPDATE ville SET hab ="'.$habInc.'" WHERE nom="'.$id.'"');	 

						// Ajout heure Click +id users + id Ville 					
								$req = $bdd->prepare('INSERT INTO inclusion(id_ville, id_users, click_hour) VALUES(:id_ville, :id_users, :click_hour)');
								$req->execute(array(
									'id_ville' => $id_ville,
									'id_users' => $_SESSION['id'],
									'click_hour' => $date
									));

						// End Ajout heure Click			
					}				
				
				}
			}
			else
			{
			    echo '<br />Veuillez attendre la fin de la construction' ;
			    $finish = false;
			}
			/* End Gestion des dates */


		}
/* End Si une ville est présente dans l'URL */
?>



<?php
if ($TempsRestant->s < 30) {
	$test1 = 60 - $TempsRestant->s;
}
else if ($TempsRestant->s > 30) {
	$test1 = abs($TempsRestant->s - 60);
}
else if ($TempsRestant->s = 30) {
	$test1 = $TempsRestant->s;
}

$heures   = $TempsRestant->h;  // les heures < 24
$minutes  = 9 - $TempsRestant->i;   // les minutes  < 60
$secondes = $test1;  // les secondes  < 60



$annee = date("Y");  
$mois  = date("m");  
$jour  = date("d"); 

$secondes = mktime(date("H") + $heures,
                            date("i") + $minutes,
                            date("s") + $secondes,
                            $mois,
                            $jour,
                            $annee
                            ) - time();
?>


<?php
$message = "Keyser Soze vous a répondu!";
?>
 
<script type="text/javascript">
var msg='<?PHP echo $test;?>';
 alert(msg);
</script>

<script type="text/javascript">

var temps = <?php echo $secondes;?>;
var valide = <?php echo $TempsRestant->i;?>;
var timer =setInterval('CompteaRebour()',1000);

if (valide > 0 && valide < 10) {
	
	function CompteaRebour(){
	  temps-- ;
	  j = parseInt(temps) ;
	  h = parseInt(temps/3600) ;
	  m = parseInt((temps%3600)/60) ;
	  s = parseInt((temps%3600)%60) ;
	  document.getElementById('minutes').innerHTML= (h<10 ? "0"+h : h) + '  h :  ' +
	                                                (m<10 ? "0"+m : m) + ' mn : ' +
	                                                (s<10 ? "0"+s : s) + ' s ';
		if ((s == 0 && m ==0 && h ==0)) {
		   clearInterval(timer);

		}
	}
}

</script>


<body onload="timer">
<?php
// la condition est que le nombre de seconde soit etre superieur a 24 heures
if ($secondes <= 3600*24) {
?>
<span>Il vous reste comme temps</span>
<div id="minutes" ></div></span>
<?php
 }
?>



</body>
</html>


login --------------------------------------


<form action="" method="post">
	<label for="email">Adresse email</label>
	<input type="text" name="email" value=""/>
	<label for="passe">Mot de passe</label>
	<input type="password" name="pass" value=""/>
	<input type="submit" value="Se connecter"/>
</form>






<?php 
	try { $bdd = new PDO('mysql:host=localhost;dbname=test;charset=utf8', 'root', ''); }
        catch(Exception $e) { die('Erreur : '.$e->getMessage()); }
	// Hachage du mot de passe
	$pass_hache = sha1($_POST['pass']);
    $email = htmlspecialchars($_POST['email']);
	// Vérification des identifiants

	$req = $bdd->prepare("SELECT id, pseudo FROM users WHERE email = '".$email."'");
	$req->execute(array(
	    'email' => $email,
	    'pass' => $pass_hache));

	$resultat = $req->fetch();

	if (!$resultat)
	{
	    echo 'Mauvais identifiant ou mot de passe !';
	}
	else
	{
	    session_start();
	    $_SESSION['id'] = $resultat['id'];
	    $_SESSION['pseudo'] = $resultat['pseudo'];
	    echo 'Vous êtes connecté !';
	}
?>

 <a href="test.php">gm</a>
 <?php 
	if (isset($_SESSION['id']) AND isset($_SESSION['pseudo']))
	{
	    echo 'Bonjour ' . $_SESSION['pseudo'];
	}
?>


--------------------------------------------------- BDD
-- phpMyAdmin SQL Dump
-- version 4.1.14
-- http://www.phpmyadmin.net
--
-- Client :  127.0.0.1
-- Généré le :  Mer 12 Août 2015 à 17:07
-- Version du serveur :  5.6.17
-- Version de PHP :  5.5.12

SET SQL_MODE = "NO_AUTO_VALUE_ON_ZERO";
SET time_zone = "+00:00";


/*!40101 SET @OLD_CHARACTER_SET_CLIENT=@@CHARACTER_SET_CLIENT */;
/*!40101 SET @OLD_CHARACTER_SET_RESULTS=@@CHARACTER_SET_RESULTS */;
/*!40101 SET @OLD_COLLATION_CONNECTION=@@COLLATION_CONNECTION */;
/*!40101 SET NAMES utf8 */;

--
-- Base de données :  `test`
--

-- --------------------------------------------------------

--
-- Structure de la table `inclusion`
--

CREATE TABLE IF NOT EXISTS `inclusion` (
  `id_ville` int(11) NOT NULL,
  `id_users` int(11) NOT NULL,
  `click_hour` datetime NOT NULL,
  PRIMARY KEY (`id_ville`,`id_users`)
) ENGINE=InnoDB DEFAULT CHARSET=latin1;

--
-- Contenu de la table `inclusion`
--

INSERT INTO `inclusion` (`id_ville`, `id_users`, `click_hour`) VALUES
(1, 36, '2015-08-04 18:31:07'),
(2, 36, '2015-08-05 15:34:39'),
(3, 36, '2015-08-04 18:25:43');

-- --------------------------------------------------------

--
-- Structure de la table `users`
--

CREATE TABLE IF NOT EXISTS `users` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `pseudo` varchar(100) NOT NULL,
  `pass` varchar(100) NOT NULL,
  `email` varchar(100) NOT NULL,
  `date_inscription` date NOT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB  DEFAULT CHARSET=latin1 AUTO_INCREMENT=38 ;

--
-- Contenu de la table `users`
--

INSERT INTO `users` (`id`, `pseudo`, `pass`, `email`, `date_inscription`) VALUES
(1, 'Patrick', 'PC', 'test@gmail.com', '0000-00-00'),
(2, 'test', 'test', 'test', '2015-07-20'),
(3, 'test', 'test', 'test', '2015-07-20'),
(4, 'test', 'test', 'test', '2015-07-20'),
(34, 'John', 'a94a8fe5ccb19ba61c4c0873d391e987982fbbd3', 'john@gmail.com', '2015-07-20'),
(35, 'Jean', 'a3da4c6307d230e1f1c8ad62e00d05ff1f1f5b52', 'kluj@gmail.com', '2015-07-20'),
(36, 'Syl', '8efd86fb78a56a5145ed7739dcb00c78581c5375', 'syl@gmail.com', '2015-07-20'),
(37, 'test', 'test', 'test', '2015-07-27');

-- --------------------------------------------------------

--
-- Structure de la table `ville`
--

CREATE TABLE IF NOT EXISTS `ville` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `nom` varchar(100) NOT NULL,
  `hab` bigint(20) NOT NULL,
  `poli` mediumint(9) NOT NULL,
  `poll` mediumint(9) NOT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB  DEFAULT CHARSET=latin1 AUTO_INCREMENT=4 ;

--
-- Contenu de la table `ville`
--

INSERT INTO `ville` (`id`, `nom`, `hab`, `poli`, `poll`) VALUES
(1, 'Nice', 192, 0, 0),
(2, 'Blagn', 229, 0, 0),
(3, 'John', 25, 0, 0);

/*!40101 SET CHARACTER_SET_CLIENT=@OLD_CHARACTER_SET_CLIENT */;
/*!40101 SET CHARACTER_SET_RESULTS=@OLD_CHARACTER_SET_RESULTS */;
/*!40101 SET COLLATION_CONNECTION=@OLD_COLLATION_CONNECTION */;
 -----------------------------------------------------
