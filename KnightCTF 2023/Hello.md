# Hello (300 pts)
___________
Pour ce challenge on nous donne une capture de réseaux après une rapide analyse, on remarque une série de requetes dns non-resolue.

On voit que leur sous domaine fais un caractère à chaque fois on les écrits les uns a la suit des autres et on obtient :

```VVBCTHtvMV9tcjNhX2VuMF9oazNfaTBofQ==```

On décode cette chaîne de caractère qui est visiblement en base 64 celas nous donne :

```UPBL{o1_mr3a_en0_hk3_i0h}```

On voit que cette chaîne est chiffrer avec un algorithme de substitution, car la structure semble correcte, on cherche le début de la clef (on nous dit dans l'énoncer que le flag est coder en ***vigenère***) car on sait que le flag commence par KCTF on obtient KNIG nous en avons déduit que la clef était KNIGHT et on obtient le flag :

```KCTF{h1_th3n_wh0_ar3_y0u}```

Résolue par cartoone et maple
