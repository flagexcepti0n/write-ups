![[Pasted image 20240413140344.png]]

Pour ce challenge, on nous donne **deux fichiers** :
- une image d'un graphe GNU Radio (pour rappel c'est un logiciel open source utilisé pour faire du traitement du signal).
- un fichier .iq. Ce format permet de stocker un signal, mais comme on est en digital (discret), on a un certain échantillonnage.

# 1. analyse du graphe

On commence par analyser le graphe GNU Radio. On y distingue 3 parties principales : 

![[simple-fm-graph-analyse.png]]

Dans la première partie, un fichier flag.txt est ouvert, puis son contenu est extrait en paire de bytes puis cette paire de bytes est convertie selon la table suivante par les blocks "char to float" et par la soustraction "- 1.5" :

| Valeur byte | Valeur float |
| :---------- | :----------: |
| 00          |    -0.03     |
| 01          |    -0.01     |
| 10          |     0.01     |
| 11          |     0.03     |
En somme, cette séquence prend les bytes deux par deux dans un fichier, et les encode en des floats compris entre -0.03 et 0.03.

La partie deux prend en entrée un float et répète cette entrée 3200 fois avent de passer à la suivante.

Enfin, dans la partie trois, ce float est envoyé dans un VCO, puis le signal obtenu est envoyé dans un fichier, en l'occurrence le fichier qui nous est fourni dans le challenge. 

Je ne connaissais pas les VCOs avant de traiter ce challenge. Après quelques recherches, j'ai compris qu'il s'agit d'un oscillateur piloté par tension (Voltage Controlled Oscillator) : il transforme une tension d'entrée en un signal à une certaine fréquence, ce qui revient à transmettre l'information en faisant de la modulation de fréquence (Frequence Modulation dont l'acronyme est connu : FM)

![[Pasted image 20240414184107.png]]

# 2. résolution

pour résoudre ce challenge j'ai utilisé deux outils principaux :
- GNU radio
- python

La première étape a été d'effectuer l'opération inverse du VCO c'est à dire de démoduler le fichier .iq avec un bloc "FM Demod".  Je récupère dans mon fichier de sortie une séquence de floats.
Le graphe est donc :

![[Pasted image 20240413153049.png]]

On dispose donc maintenant d'un fichier qui contient tous les float démodulés. On va plutôt  utiliser Python pour les décoder (GNU radio est très bien pour traiter les flux... mais pour des algos un peu plus poussés, c'est pas le plus simple à utiliser ^^'). 

Pour cela, je vais utiliser la bibliothèque numpy qui permet d'importer et de traiter ces nombres flottants :

```python
import numpy as np

values = np.fromfile("decode_data.data",dtype=np.float32)
```

La première chose que je vais faire, c'est reconnaître les parties qui sont des données et les parties qui ne sont que des transitions entre elles :

```python
def cut_frame(data) -> list[tuple]:

	last = 0
	nb = 0
	decode = []
	
	for i in data[::100]:
		a = round(i,3)
		
		if a == last:
			nb += 1
		else:
			if nb > 10:
				decode += [(last, round(nb/32))]
			
			nb = 1
			last = a
	
	return decode
```

Cette fonction prend en entrée une liste de nombres et elle fournit en sortie un liste de tuples (valeur, durée). Je prends une valeur sur 100 pour améliorer la vitesse sans perdre trop en information, et je divise le nombre de valeurs consécutives par 32 ($32\cdot100=3200$ : on retombe sur le nombre de fois qu'ont été répétées les données en entrée : c'est bien fait !)

On décode ensuite ces tuples en utilisant le tableau donné plus haut :

```python
out = ""

for i in cut_frame(values):
	if str(i[0]) == "0.03":
		out += ("11" * i[1])
	if str(i[0]) == "0.01":
		out += ("10" * i[1])
	if str(i[0]) == "-0.01":
		out += ("01" * i[1])
	if str(i[0]) == "-0.03":
		out += ("00" * i[1])
print(out)
```

Après exécution on obtient ceci :

```
01000110010000110101001101000011011110110110001100110000011001100110010100110110001100110011011000110111001100110011010001100010001110000110001100110110001100010011011100110100001100110110010001100001001110010011010000110110001110000110001101100100011000110110011000110111001110010110010001100010001100110110011000110101011000100011011101100100001100110110011000111000011001100011010100110000001101000011001100110111001110000011000101100110011000110110010001100010011000100011100101100100011000110110010001100101011001010011010101100010011000010110001101111101
```

On décode du binaire vers ASCII, et on retrouve le flag :

```
FCSC{c0fe636734b8c61743da9468cdcf79db3f5b7d3f8f5043781fcdbb9dcdee5bac}
```

# 3. Secure FM

![[Pasted image 20240413164914.png]]

Pour ce deuxième challenge, on dispose des mêmes entrées que pour le challenge "facile" :  l'image du graphe GNU radio, et un fichier au format .iq

![[Pasted image 20240413164840.png]]

Donc on repart pour un tour, et on analyse les différences avec le premier graphe :

![[secure-fm-graph-analyse.png]]

On retrouve à peu prés le même graphe pour ce qui de la première branche - à ceci prés qu'une multiplication par 0.5 a été ajoutée à l'étape (1). On doit donc réviser notre table de correspondance :

| Valeur byte | Valeur float |
| :---------- | :----------: |
| 00          |    -0.015    |
| 01          |    -0.005    |
| 10          |    0.005     |
| 11          |    0.015     |
De plus, à l'étape (3), on voit aussi que le signal qui contient les données tant désirées (le précieux...) est multiplié avec un signal sorti de la branche 2.

Dans la deuxième branche, est généré un signal aléatoire à partir d'un entier aléatoire entre 0 et 15. Malheureusement, on ne connait pas la seed... qui nous est cachée !!

Après un peu de réflexion, deux problèmes m'ont sauté aux yeux. Ils vont nous permettre de reconstituer le signal qui a été "chiffré" :
- l'ensemble des valeurs possibles avant chiffrement est très restreint
- chaque paire de bytes est répétée 3000 fois, mais la valeur aléatoire n'est répétée que 1000 fois. Une paire de bytes est donc chiffrée avec 3 signaux issus de nombres aléatoires potentiellement différents.

La solution que j'ai trouvée pour tirer profit de ces erreurs, est de générer 16 fichiers avec le graphe GNU Radio suivant :

![[Pasted image 20240413214448.png]]

Ce graphe va générer les floats en supprimant le "chiffrement". 

Bon, ça ne suffit pas, car on ne décode que selon une constante. Il faut maintenant trouver pour chaque paire de bytes laquelle des 16 valeurs garder... et c'est là qu'on ressort notre Script Python. On va quand même le modifier : on va faire en sorte que le code récupère les valeurs uniquement quand elles sont cohérentes.

De chaque séquence de 1000 éléments, puis pour chaque groupe de 3 séquences, on prend la valeur la plus présente :

```python
import numpy as np

sample = [[] for i in range(60000)]

file = "dmp/"

for _ in range(17):

    values = np.fromfile(file+str(_)+".data",dtype=np.float32)
    
    print("file load",_)

    last = 0
    nb = 0

    data = []

        
    cpt = 0
    sur = len(values)/40
    for v in values[::40]:
        cpt += 1
        a = round(v,5)
        if a == last:
            nb += 1
        else:
            if nb > 10:
                data += [(last,nb)]
            nb = 1
            last = a

    cpt = 0
    char = []
    framelist = []
    
    print(len(data))

    for j in data:
        a,b = j
        l = round(b / 25)
        if l >= 1:
            cpt += 1
            for i in range(l):
                char += [a]
                if len(char) == 3:
                    framelist += [char]
                    char = []
                    
    for i in range(len(framelist)):
        for j in framelist[i]:
            if str(round(j,3)) in ["-0.015","-0.005","0.005","0.015"]:
                sample[i] += [round(j,3)]
    print("file analyse",_)

out = ""

for w in flag:
    comptes = {}

    # Parcourir la liste et compter les occurrences de chaque élément
    for element in w:
        if element in comptes:
            comptes[element] += 1
        else:
            comptes[element] = 1

    # Initialiser les variables pour stocker l'élément le plus présent et son compte
    element_le_plus_present = None
    compte_le_plus_eleve = 0

    # Parcourir le dictionnaire pour trouver l'élément avec le compte le plus élevé
    for element, compte in comptes.items():
        if compte > compte_le_plus_eleve:
            element_le_plus_present = element
            compte_le_plus_eleve = compte
            
    frac = element_le_plus_present
    
    if str(frac) == "-0.015":
        out += "00"
    if str(frac) == "-0.005":
        out += "01"
    if str(frac) == "0.005":
        out += "10"
    if str(frac) == "0.015":
        out += "11"
        
print(out)
```

On obtient, après un certain temps...,  une suite de bytes. On la décode, et on obtient un extrait de MobyDick (haha !), et ce flag :

```
FCSC{45157c712a46090d497ce258eb534167194910ae3edbf988c0afca8c0a0bef29}
```

![[Pasted image 20240413170517.png]]