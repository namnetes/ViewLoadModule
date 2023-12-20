# ViewLoadModule
VLM signifie “View Load Module” il s'agit d'une focntion de IBM File Manager pour z/OS.

L'objectif de ce projet est de fournir un outil dédié à l'extraction spécifique de caractéristiques des modules IBM z/OS, lesquels sont stockés dans des LOADLIBS. Ce processus d'extraction découle de l'analyse préalable effectuée au moyen de la fonction **VLM d'IBM File Manager for z/OS**, constituant ainsi la source essentielle de données de ce projet.

# Structure du fichier à analyser
Un en-tête de page que l'on retrouve après chaque saut de page
```
1IBM File Manager for z/OS
```

Un en-tête de fichier avec les différentes instructions File Manager utilisées
```
0$$FILEM VLM DSNIN=EXPL.BIB.CHME.YA0.LODLIB,
 $$FILEM MEMBER=*,
 $$FILEM SUMMARY=NO,
 $$FILEM FUNCTION=PRINT,
 $$FILEM XLABEL=YES,
 $$FILEM DATEMASK=YYYY/MM/DD,
 $$FILEM SORTBY=ADDRESS
```

Un début de section &laquo;Load Module Information&raquo; suivi de diverses lignes attributs
```
  Load Module Information

   Load Library   EXPL.BIB.CHME.YA0.LODLIB
   Load Module    CCBD01
        Linked on 2003/07/21 at 11:19:58 by PROGRAM BINDER 5695-PMB V1R3
              EPA 000000 Size 0008250 TTR 000006 SSI 51EBC3AD AC 00 AM  31 RM ANY
       Attributes
```

Suit le tableau des CSECT qui compose le module. Prêtez attention à la dernière ligne qui débute par ```C'FMNBA215'``` et qui est suivie du nombre de CSECT.
```
 Name      Type Address Size    Class            A/RMODE Compiler 1                   Compiler 2                   Date
 --------- ---- ------- ------- ---------------- ------- ---------------------------- ---------------------------- ----------
 CCBD01     SD  0000000 00075EF B_TEXT           MIN/ANY Enterpr.COBOL for z/OS V3R1                               2003/07/21
 CEESG005   SD  00075F0 0000018 B_TEXT           MIN/ANY PL/X 370 V1R4                HIGH-LEVEL ASSEMBLER V1R4    2002/04/05
 CEELOCT    SD  0007608 0000014 B_TEXT           MIN/ANY HIGH-LEVEL ASSEMBLER V1R4                                 2002/04/19
 CEEBETBL   SD  0007620 0000028 B_TEXT           MIN/ANY HIGH-LEVEL ASSEMBLER V1R4                                 2002/04/19
 CEESTART   SD  0007648 00000B0 B_TEXT           MIN/ANY HIGH-LEVEL ASSEMBLER V1R4                                 2002/04/19
 IGZCBSO    SD  00076F8 0000568 B_TEXT            31/ANY PL/X 370 V1R4                HIGH-LEVEL ASSEMBLER V1R4    2002/04/05
 CEEARLU    SD  0007C60 00000B0 B_TEXT           MIN/ANY PL/X 390 V2R1                HIGH-LEVEL ASSEMBLER V1R4    2002/04/19
 CEEBPIRA   SD  0007D10 00002D8 B_TEXT           MIN/ANY PL/X 390 V2R1                HIGH-LEVEL ASSEMBLER V1R4    2002/04/19
 CEECPYRT   SD  0007FE8 00000E2 B_TEXT           MIN/ANY PL/X 390 V2R1                HIGH-LEVEL ASSEMBLER V1R4    2002/04/19
 CEEBPUBT   SD  00080D0 0000070 B_TEXT           MIN/ANY HIGH-LEVEL ASSEMBLER V1R4                                 2002/04/19
 CEEBTRM    SD  0008140 00000A4 B_TEXT           MIN/ANY HIGH-LEVEL ASSEMBLER V1R4                                 2002/04/19
 CEEBLLST   SD  00081E8 000005C B_TEXT           MIN/ANY HIGH-LEVEL ASSEMBLER V1R4                                 2002/04/19
 CEEBINT    SD  0008248 0000008 B_TEXT           MIN/ANY PL/X 390 V2R1                HIGH-LEVEL ASSEMBLER V1R4    2002/04/19
 FMNBA215 13 Control sections processed
```

## Quelques particularités
Fréquement dans les fichiers IBM Mainframe le premier carcatère de la ligne est un caractère de commande :
- "0" indique une nouvelle ligne
- "1" indique une nouvelle page

La dernière ligne __non vide__ de la section &laquo;Load Module Information&raquo; peut prendre différentes formes :
```
0      Attributes
0      Attributes RF RN RU
0      Attributes    RN RU
0      Attributes    RN RU    NX
0      Attributes       RU
0      Attributes       RU       OL
       Attributes
       Attributes             NX
       Attributes RF RN RU
       Attributes    RN RU
       Attributes    RN RU    NX
       Attributes    RN RU       OL
       Attributes    RN RU          TS
       Attributes       RU
       Attributes       RU    NX
       Attributes       RU       OL
       Attributes                   TS
```

Le tableau des CSECT peut être vide :
```
 Name      Type Address Size    Class            A/RMODE Compiler 1                   Compiler 2                   Date
 --------- ---- ------- ------- ---------------- ------- ---------------------------- ---------------------------- ----------
 FMNBA215 0 Control sections processed
```

L'information &laquo;Compiler 1&raquo; et/ou &laquo;Compiler 2&raquo; du tableau des CSECT peut ne pas être renseignée :
```
 Name      Type Address Size    Class            A/RMODE Compiler 1                   Compiler 2                   Date
 --------- ---- ------- ------- ---------------- ------- ---------------------------- ---------------------------- ----------
 AACCLOCK   SD  0000000 0007D1A B_TEXT           MIN/ANY Enterpr.COBOL for z/OS V4R1                               2019/12/16
 @ST00001   SD  0007D20 00004D0 B_TEXT           MIN/ANY C/C++ for z/OS V2R2                                       2019/12/16
 @@DLL      SD  00081F0 0000088 B_TEXT           MIN/ANY C/C++ for z/OS V2R0                                       2019/12/16
 @@PPA2     SD  0008278 000000C B_TEXT           MIN/ANY                                                           UNKNOWN
 @@DLLI     SD  0008288 000000C B_TEXT           MIN/ANY                                                           UNKNOWN
 @@XINIT@   SD  0008298 0000004 B_TEXT           MIN/ANY Enterpr.COBOL for z/OS V4R1                               2019/12/16
 @@INIT@    SD  00082A0 00004FC B_TEXT           MIN/ANY Enterpr.COBOL for z/OS V4R1                               2019/12/16
 DSNAA      SD  00087A0 0000100 B_TEXT            31/ANY PL/X 390 V2R4                HIGH-LEVEL ASSEMBLER V1R6    2010/01/08
 DSNTIAR    SD  00088A0 00002B9 B_TEXT            31/ANY HIGH-LEVEL ASSEMBLER V1R6                                 2010/01/08
 DSNALI     SD  0008B60 0000310 B_TEXT            31/ANY PL/X 390 V2R4                HIGH-LEVEL ASSEMBLER V1R5    2010/08/19
 DSNHADD2   SD  0008E70 0000058 B_TEXT            31/ANY HIGH-LEVEL ASSEMBLER V1R6                                 2010/01/08
 @@GETFN    SD  0008EC8 000000A B_TEXT           MIN/ANY HIGH-LEVEL ASSEMBLER V1R6                                 2015/03/04
 DSNHADDR   SD  0008ED8 0000058 B_TEXT            31/ANY HIGH-LEVEL ASSEMBLER V1R6                                 2010/01/08
 DSNHMVHW   SD  0008F30 000001C B_TEXT            31/ANY HIGH-LEVEL ASSEMBLER V1R6                                 2010/01/08
 CBCSG003   SD  0008F50 0000050 B_TEXT           MIN/ANY HIGH-LEVEL ASSEMBLER V1R6                                 2015/03/05
 @@C2CBL    SD  0008FA0 000000A B_TEXT           MIN/ANY HIGH-LEVEL ASSEMBLER V1R6                                 2015/03/04
 CEESG003   SD  0008FB0 000012B B_TEXT           MIN/ANY HIGH-LEVEL ASSEMBLER V1R6                                 2015/03/04
 CEESG005   SD  00090E0 0000018 B_TEXT           MIN/ANY PL/X 390 V2R4                HIGH-LEVEL ASSEMBLER V1R6    2015/10/01
 CEEBETBL   SD  00090F8 0000028 B_TEXT           MIN/ANY HIGH-LEVEL ASSEMBLER V1R6                                 2015/03/04
 CEESTART   SD  0009120 00000B0 B_TEXT           MIN/ANY HIGH-LEVEL ASSEMBLER V1R6                                 2015/03/04
 IGZCBSO    SD  00091D0 0000580 B_TEXT            31/ANY PL/X 390 V2R4                HIGH-LEVEL ASSEMBLER V1R6    2015/03/04
 CEEARLU    SD  0009750 00000B8 B_TEXT           MIN/ANY PL/X 390 V2R4                HIGH-LEVEL ASSEMBLER V1R6    2015/03/04
 CEEBPIRA   SD  0009808 00002A0 B_TEXT            31/ANY PL/X 390 V2R4                HIGH-LEVEL ASSEMBLER V1R6    2015/03/04
 CEECPYRT   SD  0009AA8 00000E2 B_TEXT           MIN/ANY PL/X 390 V2R4                HIGH-LEVEL ASSEMBLER V1R6    2015/03/04
 CEEBPUBT   SD  0009B90 0000070 B_TEXT           MIN/ANY HIGH-LEVEL ASSEMBLER V1R6                                 2015/03/04
 CEEBTRM    SD  0009C00 00000A4 B_TEXT           MIN/ANY HIGH-LEVEL ASSEMBLER V1R6                                 2015/03/04
 CEEBLLST   SD  0009CA8 000005C B_TEXT           MIN/ANY HIGH-LEVEL ASSEMBLER V1R6                                 2015/03/04
 CEEBINT    SD  0009D08 0000008 B_TEXT           MIN/ANY PL/X 390 V2R4                HIGH-LEVEL ASSEMBLER V1R6    2015/03/04
 FMNBA215 28 Control sections processed
```

# vlm.py

Il correspond au nom du programme Python à exécuter. Ce script accepte les paramètres suivants :

- Le nom du fichier résultant de l'analyse File Manager effectuée avec la fonction VLM.  
  Par défaut, le chemin est ```./vlm.txt```.

- Un commutateur pour exécuter le programme Python en mode silencieux.  
  Par défaut, une barre de progression est affichée.

- L'aide en ligne.

Exemple :
```
(.venv) galan@TATOOINE:~/Workspace/py/vlm$ python vlm.py -h
usage: vlm.py [-h] [-f INPUT_FILE] [-q]

TODO: Ajoutez une description du script.

options:
  -h, --help            show this help message and exit
  -f INPUT_FILE, --input_file INPUT_FILE
                        Chemin vers le fichier à traiter.
  -q, --quiet           Désactiver l'affichage de la barre de progression.
```