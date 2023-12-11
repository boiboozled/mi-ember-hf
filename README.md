# mi-ember-hf - Madárhatározó+

## Tartalom
[Motiváció](https://github.com/boiboozled/mi-ember-hf#Motiváció)

[Feladat](https://github.com/boiboozled/mi-ember-hf#Feladat)

[Adatok](https://github.com/boiboozled/mi-ember-hf#Adatok)

- [Az adathalmazról](https://github.com/boiboozled/mi-ember-hf#[az-adathalmazról)
  
- [Adatelőkészítés](https://github.com/boiboozled/mi-ember-hf#Adatelőkészítés)
  
[Model Tanítás](https://github.com/boiboozled/mi-ember-hf#Model-Tanítás)

- [Transfer learning](https://github.com/boiboozled/mi-ember-hf#transfer-learning)
  
- [Ensemble Learning](https://github.com/boiboozled/mi-ember-hf#ensemble-Learning)
  
[Konklúzió](https://github.com/boiboozled/mi-ember-hf#Konklúzió)

## Motiváció
A házi feladatom motivációja a Magyar Madártani és Természetvédelmi Egyesület által készített alkalmazás, a [Madárhatározó](https://mme.hu/madarhatarozo-mobiltelefonos-alkalmazas-0). Ahogy a neve is sugallja, az alkalmazás fő célja, hogy segítse beazonosítani a madárfajtákat az arra kiváncsiaknak. Jelenleg ezt három sliderrel lehet megtenni, ahol ki kell választani a látott madár alakját, tollazata színét és az észlelés helyszínét. A célom az volt, hogy egy olyan képfelismerő modellt készítsek, ami megkönnyíti ezt a folyamatot, és az alkalmazás egy új funkciójának alapját képezheti.

## Feladat
A félév során elvégzendő feladatom az volt, hogy egy olyan képfelismerő modellt készítsek, ami kellően pontosan tud madárfajtákat felismerni. Ezt a célt transfer learning és ensemble learning segítségével kívántam elérni. Transfer learning esetén egy már betanított modellt tanítunk újra más adatokon, egy új feladatra. Ensemble learning esetén pedig több modell kimenetét használjuk fel, hogy előállítsuk az új kimenetet.

## Adatok
### Az adathalmazról
A feladatomhoz a [CUB-200-2011](https://data.caltech.edu/records/65de6-vp158) adathalmazt használtam, amiben 200 madárfajtáról összesen 11 788 kép található, ezek közel fele, 5 994 tanuló-, a maradék 5 794 pedig teszt minta. A feladat megoldása során a teszt halmazt használtam validációra és tesztelésre egyaránt.
### Adatelőkészítés
Az adatokon a képfelismerési feladatoknál megszokott előkészítési és adatbővítési lépéseket végeztem el.
Minden adaton elvégzett, determinisztikus lépések:
- Újraméretezéss fix méretre,
- Középre vágás,
- Normalizáció
Csak a tanító adatokon elvégzett, véletlenszerű adatbővítési lépések:
- Véletlenszerű kivágás,
- Horizontális és vertikális forgatás,
- Szín jitter

## HW/SW környezet
A munkám során Google Colab környezetben dolgoztam. A tanítás gyorsítása érdekében GPU-s környezetben (T4 GPU, 15GB RAM). A Google colabban alapból megtalálható könyvtárakon kívül a PyTorch Lightning és WandB könyvtárakat használtam.

## Model Tanítás
### Transfer learning
A feladat transfer learning része során több modellt is megvizsgáltam, hogy hogyan teljesítenek az adathalmazomon. Mindegyiket ugyan azzal a módszerrel: az utolsó, kimeneti réteget egy FC (Fully Connected) réteggel helyettesítettem, aminek 200 kimenete volt, pontosan egy, minden osztálynak megfeleltetve. Magát a tanítást elvégeztem úgy is, hogy a már betanított modellek súlyait befagyasztottam és csak a kimeneti réteget tanítottam, illetve úgy is, hogy minden súly állítható volt a tanítás során. Ezutóbbi módszer rendre erősebbnek bizonyult. 
A megvizsgált modellek:
- Alexnet
- ResNet-101
- Efficientnet-b2
- Squeeznet1-1
- Mobilnet v2
- Densenet-121
A fenti modellek közül a két legjobban teljesítő a ResNet-101 és Densenet-121 voltak, ezért ezekkel folytattam a munkám. Mind a két modellnek két verzióját tartottam meg, mert a wandb söprések (sweep) során az látszott, hogy a modellek hasonló eredményt érnek el Adam és AdamW optimalizálókkal, mint SGD-vel, csak sokkal hamarabb kezdenek el túltanulni a tanító adatokon. Mind a négy modell verzió kb 57%-os pontosságot ért el a validációs halmazon.
### Ensemble Learning
Transfer learning során a betanított modellek kimenetét egyetlen FC réteggel alakítottam át 200 osztályú kimenetté. Az ensemble modellt vizsgáltam úgy is, hogy két modell kimenetein tanítottam, és úgy is, hogy mind a négy modellén. A két modelles esetekben minden elképzelhető permutációt megvizsgáltam, SGD és Adam optimalizálóval is. Végül a legjobb eredmény akkor jött ki, amikor mind a Denset-121, mind a ResNet-101 SGD-vel optimaliz
lt verzióját használtam az ensemble modell tanítására, amit szintén SGD-vel optimalizáltam. Azonban ennek a modellnek is csak 60%-os pontosságot sikerült elérnie, ami azon kívül, hogy rendkívül alacsony, alig múlja felül az alap modellek teljesítményét.

## Konklúzió
A házi feladatom során madárfajták osztályozásával foglalkoztam, célom az volt, hogy egy kellően erős modellt tanytsak ahhoz, hogy az egy képfelismerő funkció alapját képezhesse akár aMadárhatározó nevű applikáció kiegészítéseként. A munkám során a CUB-200-2011 adathalmazzal dolgoztam, amin előre betanított modelleket tanítottam transfer learning segytségével. A legjobban teljesítő modelleket felhasználva aztán egy ensemble modellt tanítottam, ami ugyan felül tudta múlni az újratanított modelleket, de így is jócskán elmaradta kívánt pontosságtól. A végső 60%-os pontosságú modell ugyanis nem elég jó ahhoz, hogy egy képfelismerő funkció biztos alapját képezze.

## Szerző
Mihályi Balázs Márk
