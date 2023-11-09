# Des p'tits trous (misc - 298)

dans ce challenge on nous fournis des carte perforer avec la machine IBM-029 cette technique de carte perforer servais a stocker des donner dans les temps reculer ou la mémoire de masse n'avais pas encore vue le jour les image qui nous sont fournit ressemble a ça :

<img src="https://raw.githubusercontent.com/cartoone222/rootme/main/0000000004.jpg" />

après quelque recherche je suis tomber sur la page web suivent :

http://laighside.com/punchcard.htm

mais il nous maque les réglage après quelque recherche sur la perforeuse IBM-029 on finit par trouver un format de carte qui correspond au notre **80x12** on applique ces réglage et effectivement on décode notre premier carte ensuit j'ai utiliser ce script python avec globalement les même réglage pour décoder de manier automatique tout les autre carte :

https://github.com/digitaltrails/punchedcardreader

une fois tout les carde décoder on obtiens ce qui s’apurent a un programme informatique on fait une rapide recherche historique et on, découvre qu'il y as 70 ans a l’époque de sortie de la machine en question deux langage de programmation dominais le marcher le **cobolte** et le **fortran** on remarque aussi que dans le programme que nous venons de décoder il y as des commentaire et qu'il commence tous par un "!" fort de cette information on peut en conclure que le langage du programme mystérieux est le **fortran**. 

la deuxième partie du défi en outre le décodage a étais de remettre dans l'ordre le programme il est baser sur **4** boucle équivalent a notre boucle for et utilise comme index la variable i on peut donc classer les instruction en deux catégorie celle qui utilise i dans leur syntaxe et les autre. dans les ligne qui n'utilise pas i on remarque un certain nombre de ligne de set-up qui serve a déclarer les variable ou a inclure un jeux de donner on les palace donc en haut on vois aussi 3 instruction qui vont finie le programme un équivalent de ce qui serais en python un printe("") , une instruction qui serais l’équivalent du return 0 en C, et enfin une instruction qui indique au compilateur la fin du programme. grâce a ces déduction on a reconstruit une bonne partie du programme il nous reste le corps du programme a reconstruire je suis partie du postulat que les boucle n’étais pas imbriquer ensuit j'ai mit les instruction dans l’ordre suivent dans la section suivent j'utiliserais des abréviation:

SSS = S1
SS = SSS
SSS = S2
SSSS = SSS

on commence par les mettre chacun dans une boucle puis on rajout l’instruction write dans la dernier pour afficher le résultat on exécutée le code qui ressemble maintene a ça :

```Fortran
! --------------------------------------------------       
PROGRAM HELLO
USE HELLO_1
IMPLICIT NONE                                           
901 FORMAT (99A)                                                                 

INTEGER :: SSS(0:255)                                                          
CHARACTER :: SSSS(0:255)  
INTEGER :: I                    
! - - - - - -    

DO I = 0,29,1                                                                  
SSS(I:I) = S1(SS(I+1)+1:SS(I+1)+1) 
END DO

DO I = 0,29,1  
SS(H(I+1)+1:H(I+1)+1) = SSS(I:I)
END DO       

DO I = 0,29,1   
SSS(I:I) = S2(SS(I+1)+1:SS(I+1)+1)
END DO          
! - - - - - -                                                                    

DO I = 0,29,1   
SSSS(I:I) = CHAR(SSS(I:I))   
WRITE (*,901,ADVANCE='NO') SSSS(I:I)  
END DO   

WRITE (*,901,ADVANCE='YES') '' 

STOP 0  
END PROGRAM  
   
! --------------------------------------------------  
```

et il nous retourne :

```
FCSC{#!F0RTR4N_1337_FOR3V3R!}
STOP 0
```



# Tri {très} sélectif (intro/misc - 20/103)

dans ce challange nous devont implementer un algorythme de tri optimiser a distance pour cela j'ai choisise d'impement la thequnique dit du tri fusion de manier recurcive.

```python
#!/usr/bin/env python3
from pwn import *

# Paramètres de connexion
HOST, PORT = "challenges.france-cybersecurity-challenge.fr", 2052

def comparer(x, y):
	io.sendlineafter(b">>> ", f"comparer {x} {y}".encode())
	return int(io.recvline().strip().decode())

def echanger(x, y):
	io.sendlineafter(b">>> ", f"echanger {x} {y}".encode())

def longueur():
	io.sendlineafter(b">>> ", b"longueur")
	return int(io.recvline().strip().decode())

def verifier():
	io.sendlineafter(b">>> ", b"verifier")
	r = io.recvline().strip().decode()

	if "flag" in r:
		print(r)
	else:
		print(io.recvline().strip().decode())
		print(io.recvline().strip().decode())

def triFusion(L):
    if len(L) == 1:
        return L
    else:
        return fusion( triFusion(L[:len(L)//2]) , triFusion(L[len(L)//2:]) )
    
def fusion(A,B):
    if len(A) == 0:
        return B
    elif len(B) == 0:
        return A
    elif comparer(A[0],B[0]):
        return [A[0]] + fusion( A[1:] , B )
    else:
        return [B[0]] + fusion( A , B[1:] )

def trier(N):
    L = longueur()

    T = [i for i in range(L)]
    T = triFusion(T)
        
    print("Le test de tri :            ",T)
    
    T2 = [i for i in range(L)]

    for i in T:
        o = T2.index(T[i])
        echanger(i,o)
        T2[i],T2[o] = T2[o],T2[i]   
        print(o,T[i])

io = remote(HOST, PORT)

N = longueur()

trier(N)

verifier()

io.close()
```

avec ce code on obtiens les deux flag:

tri sélectif : 

***FCSC{e687c4749f175489512777c26c06f40801f66b8cf9da3d97bfaff4261f121459}***

tri très sélectif :

***FCSC{6d275607ccfba86daddaa2df6115af5f5623f1f8f2dbb62606e543fc3244e33a***}

# Lapin blanc (side-channel and fault attacks - 176)

pour ce chalange des la premier fois ou je me suis connecter au serveur et entrer ma premier phrase j'ai compris que l'objectifs etais de fair une timing attaque car le temse de calcule est aficher en claire dans la comunication il ne restais plus qu'as fair un script pour automatiser le processuce. 

```python
from pwn import *

HOST, PORT = "challenges.france-cybersecurity-challenge.fr", 2350

io = remote(HOST, PORT)

rep = b""

while not b"Answer: " in rep:
	rep = io.recv()
	print(rep.decode(), end='')
	print()

flag = ""

dic = '0123456789abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ!"#$%&\'()*+,-./:;<=>?@[\\]^_`{|}~ '

while True:
	temps = [0]*len(dic)
	for i in range(len(dic)):
		io.sendline((flag + dic[i]).encode())
		rep = b""
		tt = ""
		while not b"Answer: " in rep:
			rep = io.recv()
			tt += rep.decode()
		T1 = int(tt.split("]")[0].split("[")[1])
		T2 = int(tt.split("]")[1].split("[")[1])
		
		DT = T2-T1
		temps[i] = DT
	flag += dic[temps.index(max(temps))]
	print(flag)
```

mon script ne gere pas le cas ou la phrase est corecte donc il n'affiche que la phrase avec le dernier caracter en moin avent de planter mais le dernier caracter est facilment deductible de plus comme la phrase ne change pas il sufit de relancer un conaction et d'entres manuallment le flag qui est :

***FCSC{t1m1Ng_1s_K3y_8u7_74K1nG_u00r_t1mE_is_NEce554rY}***

# Fibonacci (hardware - 178)
pour cette série de challenge on nous demande d’écrire des code en assembleur pour le premier challenge mon algorithme étais des plus simple(en même temps vue le problème ^^)

il est a noter que j'utilise le registre de travaille 6 comme valeur de référence pour les --

```
R5 --
R1 = 0
R2 = 1
R4 = 0

do
{
R3 = R2 + R1
R1 = R2
R2 = R3

R5 --
} while (R5 != 0)

R0 = R3

exit
```

```
MOV R6,#1
SUB R5,R5,R6
MOV R1,#0
MOV R2,#1
MOV R6,#1
MOV R4,#0
JR A
A:
ADD R3, R2, R1
MOV R1,R2
MOV R2,R3
SUB R5,R5,R6
CMP R5,R4
JZR B
JR A

B:
MOV R0,R3
STP
```

ce qui nous donne le code machine : 

```
800600014dad80010000800200018006000180040000CF004a53002100324dad0645C701CFF900301400
```

ce qui nous permet d'obtenire le flag : 

***FCSC{770ac04f9f113284eeee2da655eba34af09a12dba789c19020f5fd4eff1b1907}***

par cartoone
