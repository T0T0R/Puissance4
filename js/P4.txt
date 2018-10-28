/*** INITIALISATION ***/
var JEU = [[0,0,0,0,0,0,0],[0,0,0,0,0,0,0],[0,0,0,0,0,0,0],[0,0,0,0,0,0,0],[0,0,0,0,0,0,0],[0,0,0,0,0,0,0]];	//JEU = [ligne0, ligne1, ligne2, ligne3, ligne4, ligne5]
var player=1;
var cote=70;	//La largeur de la case, a changer en fonction de l'affichage


while (true){	//Boucle principale
	player=1;	//Premier demi-cycle
	effaceEcranG(0);
	AfficheGrille();
	SaisieColonne();
	if (Termine()){
		AfficheGrille();
		break;
    }
	
	player=2;	//Deuxième demi-cycle
	effaceEcranG(0);
	AfficheGrille();
	SaisieColonne();
	if (Termine()){
		AfficheGrille();
		break;
    }
}


function AfficheGrille(){
	effaceEcranG(0);
	RectanglePlein(0,0,7*cote+7,6*cote+6,'black');
	Ligne(1,0,0,6*cote+6,'white');

	for (var i = 0; i < 6; i++) {
		Ligne(0,i*cote+i,cote*7+7,i*cote+i,'white');
		for (var j = 0; j < 7; j++) {
			Ligne((j+1)*cote+j,0,(j+1)*cote+j,6*cote+6,'white');
			if (JEU[i][j]==1){
				CerclePlein(cote*(j+0.5)+j,cote*(i+0.5)+i,cote-10,'red');
			}else if (JEU[i][j]==2){
				CerclePlein(cote*(j+0.5)+j,cote*(i+0.5)+i,cote-10,'yellow');				
			}
		}

	}
}


function afficheTexteG(texte){
	effaceEcranG(7*cote);
	Texte(10, 8*cote, texte, 'black');
}


function effaceEcranG(hauteur){
	RectanglePlein(0,hauteur,10*cote,hauteur,'white'); 
}


function AfficheGrilleDebug(){
	for (var i = 0; i < 6; i++) {
		var ligne ="";
		for (var j = 0; j < 7; j++) {
			if (JEU[i][j]==0){
				ligne +=String("_");
			}else if (JEU[i][j]==1){
				ligne +=String("1");
			}else if (JEU[i][j]==2){
				ligne +=String("2");
			}
		}
		Ecrire(ligne);
	}
}


function SaisieColonne(){
	var flagSaisir = 1;
	if (player===1){
		var str="rouge";
    }else{
		var str="jaune";
    }
	while (flagSaisir===1){
		var colonne = SaisieEntier("Joueur "+str+", entrez la colonne de votre choix :")-1;
		for (var i = 0; i < 6; i++) {
			if (JEU[5-i][colonne]===0) {	//Si la case (6-i,colonne) est libre
				JEU[5-i][colonne]=player;
				flagSaisir = 2;	//Si la saisie est correcte, sortir de la boucle
				break;
			}else if (colonne<0 ||colonne>8){	//Si le numero de la colonne est incorrect
				afficheTexteG("Entrée éronnée");
				break;
			}else if (i===5) {	//Si la case (5,colonne) n'est pas vide
				afficheTexteG("Colonne pleine");
				break;
			}			
		}
	}
}


function Termine(){
	//Pour Rouge:
	if (verifLigne(1)){
		effaceEcranG(0);
		afficheTexteG("Le joueur rouge a gagné");
		return true;
	}
	if (verifColonne(1)){
		effaceEcranG(0);
		afficheTexteG("Le joueur rouge a gagné");
		return true;
	}
	if (verifDiag(1)){
		effaceEcranG(0);
		afficheTexteG("Le joueur rouge a gagné");
		return true;
	}
	//Pour jaune:
	if (verifLigne(2)){
		effaceEcranG(0);
		afficheTexteG("Le joueur jaune a gagné");
		return true;
	}
	if (verifColonne(2)){
		effaceEcranG(0);
		afficheTexteG("Le joueur jaune a gagné");
		return true;
	}
	if (verifDiag(2)){
		effaceEcranG(0);
		afficheTexteG("Le joueur jaune a gagné");
		return true;
	}

	var vide=0;
	for (var i = 0; i < 6; i++) {
		for (var j = 0; j < 7; j++) {
			if (JEU[i][j]===0){vide++;}	//Si la premiere case d'une ligne n'est pas celle du joueur, alors le compteur de jetons du joueur dans une ligne est remis a 0.
		}
	}
	if (vide ===0){	//Si vide=0, alors le tableau est plein.
		afficheTexteG("Plus de place");
		return true;
	}
	return false;
}


function verifLigne(player){
	for (var i = 0; i < 6; i++) {
		for (var j = 0; j < 4; j++) {	//On va de la colonne 1 a la 4.
			if (JEU[i][j]===player && JEU[i][j+1]===player && JEU[i][j+2]===player && JEU[i][j+3]===player){return true;}	//Si il existe une serie de 4 valeurs identiques.
		}
	}
	return false;
}


function verifColonne(player){
	for (var j = 0; j < 7; j++) {
		for (var i = 0; i < 3; i++) {
			if (JEU[i][j]==player && JEU[i+1][j]==player && JEU[i+2][j]==player && JEU[i+3][j]==player){return true;}	//Si il existe une serie de 4 valeurs identiques.
		}
	}
	return false;
}


function verifDiag(player){
	//Dans un sens (Haut-gauche vers Bas-droite)
	for (var j=1; j<=3; j++){	//Ligne appartenant a la diagonale a tester.
		for (var k=0; k<=2; k++){	//Deplace le test de k unites dans la diagonale
			if (j+0+k>6 || j+1+k>6 || j+2+k>6 ||j+3+k>6){continue;}	//Si le test tente d'acceder a des cases inexistantes
			if (JEU[0+k][j+0+k]==player && JEU[1+k][j+1+k]==player && JEU[2+k][j+2+k]==player && JEU[3+k][j+3+k]==player){return true;}	//Si il existe une serie de 4 valeurs identiques.
		}
	}
	for (var i=0; i<=2; i++){
		for (var k=0; k<=1; k++){
			if (i+0+k>5 || i+1+k>5 || i+2+k>5 || i+3+k>5){continue;}
			if (JEU[i+0+k][0+k]==player && JEU[i+1+k][1+k]==player && JEU[i+2+k][2+k]==player && JEU[i+3+k][3+k]==player){return true;}
		}
	}

	//Dans l'autre sens (Bas-gauche vers Haut-droite)
	for (var i=3; i<=5; i++){
		for (var k=0; k<=2; k++){
			if (i-0-k<0 || i-1-k<0 || i-2-k<0 || i-3-k<0){continue;}
			if(JEU[i-0-k][0+k]==player && JEU[i-1-k][1+k]==player && JEU[i-2-k][2+k]==player && JEU[i-3-k][3+k]==player){return true;}
		}
	}
	for (var j=1; j<=3; j++){
		for (var k=0; k<=2; k++){
			if(j+0+k>6 || j+1+k>6 || j+2+k>6 || j+3+k>6){continue;}
			if(JEU[5-k][j+0+k]==player && JEU[4-k][j+1+k]==player && JEU[3-k][j+2+k]==player && JEU[2-k][j+3+k]==player){return true;}
		}
	}
	return false;
}
