# Hello

Pour ce challange on nous donne une capture de reseaux apres une rapide analyse on remarque une seris de requet dns 
non-resolue.

on vois que leur sous domaisn fais un caracter a chaque fois on les ecris les un a la suit des autre et
on obtien : ```VVBCTHtvMV9tcjNhX2VuMF9oazNfaTBofQ==```

On decode cette chaine de caracter qui est visiblement en base 64 
celas nous donne : ```UPBL{o1_mr3a_en0_hk3_i0h}```

on vois que cette chaine est chiffrer avec un algorythme de substitution car la structure semble corecte
on cherche le debut de la clef car on sais que le flag comance par KCTF 
on obtien KNIG nous en avont deduit que la clef etais KNIGHT et on obtien le flag :
```KCTF{h1_th3n_wh0_ar3_y0u}```

resolue par cartoone et maple
