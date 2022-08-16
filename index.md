# Visualisointiverkosto 17.8.2022

Tälle sivulle on koottu esimerkki miten teemakartan teosta [riistaonnettomuudet-tilaston](https://www.stat.fi/tup/kokeelliset-tilastot/riistaonnettomuudet/index.html) tilastoaineistolla


## 2. Teemakartta Qgis-karttaohjelmalla

### Kartan luominen
Luodaan teemakartta, jossa maakunnittainen riistaonnettomuuksien määrä on suhteutettu liikennemäärään. 

1. Ladataan aineistot tilastovuodelle 2020 [riistaonnettomuuksien tietokantataulukosta](https://pxnet2.stat.fi:443/PXWeb/sq/c3064768-9ed1-4e6c-b90b-a095ea25b13b) ja [tietilaston tietokantataulukosta](https://pxnet2.stat.fi:443/PXWeb/sq/64b0f6c1-eda4-4653-a884-b8ce728f7028). Ladatessa valitaan muodoksi "Lataa puolipiste-eroteltu csv-tiedosto (otsikollinen)"
<img src="kuvat\lataa_puolipiste.png">

2. Muokataan csv-tiedoston otsikoita ja dataa siten että poistetaan maakuntien nimistä MK-etuliite ja lisätään koodi-muuttuja, jossa on pelkkä maakuntakoodi:

Alkuperäinen:
```
"12jz -- Maanteiden pituus ja liikennesuorite kunnittain, 2017-2020"

"Alue";"Vuosi";"Liikennesuorite"
"MK01 Uusimaa";"2020";7657
"MK02 Varsinais-Suomi";"2020";2960
...
```

Muokattu:
```
"12jz -- Maanteiden pituus ja liikennesuorite kunnittain, 2017-2020"

"Koodi";"Alue";"Tieluokka";"Päällyste";"Vuosi";"Liikennesuorite"
"01";"Uusimaa";"Yhteensä";"Yhteensä";"2020";7657
"02";"Varsinais-Suomi";"Yhteensä";"Yhteensä";"2020";2960
...
```
3. Tuodaan muokatut csv-tiedostot Qgis-karttaohjelmaan, "Layer - Taso" - valikon kautta -> Add Layer -> Add Delimited Text File
<img src="kuvat\import.png">

4. Lisätään ensin Tilastokeskuksen WFS-rajapinta ([Tilastokeskus avoimet paikkatietoaineistot](https://www.stat.fi/org/avoindata/paikkatietoaineistot.html) Qgis-karttaohjelmaan, "Layer - Taso" - valikon kautta -> Add Layer -> Add WFS Layer -> New -> Annetaan nimeksi "Stat" ja URL-kenttään liitetään kuntapohjaisten tilastoalueiden WFS-rajapinta-osoite: "https://geo.stat.fi/geoserver/tilastointialueet/wfs"
<img src="kuvat\wfs.png">

5. Painetaan Connect ja valitaan listalta Maakunnat 2020 -karttataso ja painetaan Add.
<img src="kuvat\maakunnat.png">

6. Liitetään tilastotiedot karttatasoon painamalla oikeaa hiiren painiketta tilastointialueet-tason päällä ja valitaan "Joins". Painetaan plus-painiketta ja painetaan OK, toistetaan kunnes sekä riistaonnettomuus-taso (001-alkuinen) ja liikennesuorite-taso (004-alkuinen) on liitettynä maakuntatasoon.
<img src="kuvat\properties.png">

7. Mennään Symbology-välilehdellä ja valitaan visualisointityyliksi "Graduated". Painetaan Value-rivin kohdalla olevaa "Epsilon" -painiketta
<img src="kuvat\join.png">

8. Valitaan Fields and values - kentästä 001-taulun lukumäärä ja 004-taulun liikennesuorite. Liikennesuoritteen yksikkö on miljardi autokilometriä. Jaetaan se kymmennellä, jotta saadaan jakajan yksiköksi 100 000 autokilometriä. Lisätään myös to_int-funktiot muuttujien eteen, koska ne on luettu sisään merkkimuotoisina.
<img src="kuvat\kaava.png">

9. Painetaan Classify-painiketta, vaihdetaan Mode-kentästä Pretty Breaks ja valitaan color rampi-valikosta Magma. Valitaan samasta valikosta vielä Invert color ramp, koska tumma väri sopii paremmin kuvaamaan alueita, jossa paljon onnettomuuksia. Painetaan OK ja nyt maakunnat on luokiteltu.
<img src="kuvat\classify.png">

### Kartasta kuvioksi

Jotta kartta on ymmärrettävä lisätään teemakarttakuvioon vielä otsikko, lähteet ja selite. Tätä varten käytetään ohjelman sisään rakennettua tulostus-editoria.

1. Lisätään uusi tulostus painamalla Project -> New Print Layout -> ja annetaan sille nimi
<img src="kuvat\print_layout.png">

2. Käännetaan tulostuspohja pystysuoraan valitsemalla oikean laidan valikosta Item properties ja orientation-kohdasta Portrait
<img src="kuvat\portrait.png">

3. Lisätään kartta painamalla Add item ja Add Map. "Piirretään" "paperin" kokoinen laatikko. Karttakuvan skaalausta voi säätää oikean laidan valikosta, mutta nopeinta on zoomata ja liikuttaa karttakuvaa pääikkunassa ja päivittää muutokset tulostuseditoriin painamalla Set Map Extent to Match Main Canvas Extent
<img src="kuvat\Extent.png">

4. Lisätään selite painamalla Add item ja Add Legend. Piirretään laatikko siihen kohtaan, johon selite halutaan sijoittaa
<img src="kuvat\classify.png">

5. Muotoillaan selitteen otsikko ottamalla ensin ruksi pois Auto Update -kohdasta ja sen jälkeen tuplaklikkaamalla otsikkoa. 
<img src="kuvat\auto_update.png">

7. Lisätään otsikoksi "Onnettomuudet / 100 000 autokilometriä".
<img src="kuvat\legend-otsikko.png">

8. Piilotetaan tilastotasot 
<img src="kuvat\piilotus.png">

9. Lisätään otsikko Add item ja Add Label. Lisätään otsikoksi "Riistaonnettomuudet suhteessa liikennesuoritteeseen maakunnittain Manner-Suomessa vuonna 2020". Font-valikosta voi muokata tekstin kokoa ja tyyliä.
<img src="kuvat\Otsikko.png">

10. Lisätään lähteet samaan tapaan kuin otsikko.

11. Tallennetaan lopputulos kuvaksi Layout -> Export as Image


## R Riistaonnettomuudet eläinlajeittain, maakunnittain ja kuukausittain

1. Ladataan pohjadataksi maakunnittainen riistaonnettomuusaineisto PX-Web-taulukosta. Ladataan tiedot eläinlajeittain, maakunnittian ja kuukasitasolla. Tallennetaan tiedot relaatiotiedostoon, jotta tietoja on helppo käsitellä seuraavassa vaiheessa. Jätetään yhteensä-luokat pois.
<img src="kuvat\relaatiotiedosto2.png">

Datan voi ladata myös [täältä](https://github.com/mkokkone/visualisointikoulutus/blob/gh-pages/001_12sg_2021m12_20220311-154122.txt):


2. Asennetaan ggplot2-kirjasto: Packages -> Install packages ja valitaan listalta ggplot2
3. Avataan R ja avataan uusi skripti: FIle -> New script
4. Ladataan ggplot2-kirjasto
```
library(ggplot2)
```
5. Luetaan aineisto sisään nimelle riista
```
riista <- read.delim("/001_12sg_2021m12_20220225-112559.txt")
```
6. Tehdään kuukausittain peruskuva ggplotilla, ensimmäisessä blokissa määritellään: aineisto = riista, x=kuukausi ja y=Riistaonnettomuuudet eli onnettomuuksien lukumäärä, fill=Osallinen.eläinlaji eli pylväät täytetään osallisten eläinlajien mukaan. Jälkimmäisessä osiossa määritellään että halutaan pylväsdiagrammi: geom_bar ja halutaan laskea tapauksien lukumäärä: stat = "identity".
```
ggplot(riista , aes(Kuukausi, Riistaonnettomuudet, fill=Osallinen.eläinlaji)) +    geom_bar(stat = "identity")
```
<img src="kuvat\kuva1.png">

7. Jaetaan tiedot maakunnittaisiin kuviin lisäämällä ensimmäisen rivin loppuun + -merkki ja facet_wrap -koodi, jossa kerrotaan että tiedot jaetaan useampaan kuvaan Alue-muuttujan avulla
```
ggplot(riista , aes(Kuukausi, Riistaonnettomuudet, fill=Osallinen.eläinlaji)) +    geom_bar(stat = "identity")+
facet_wrap(vars(Alue))
```

<img src="kuvat\kuva2.png">

8. Maakuntien erot onnettomuusmäärissä ovat isoja, joten vapautetaan akseleiden skaalaus, scales = "free" -optiolla

```
ggplot(riista , aes(Kuukausi, Riistaonnettomuudet, fill=Osallinen.eläinlaji)) +    geom_bar(stat = "identity")+
facet_wrap(vars(Alue), scales = "free")
```

<img src="kuvat\kuva3.png">

9. X-akselin otsikot menevät päällekkäin toistensa kanssa, käännetään niitä 45 astetta: angle-optiolla

```
ggplot(riista , aes(Kuukausi, Riistaonnettomuudet, fill=Osallinen.eläinlaji)) +    geom_bar(stat = "identity", na.rm=TRUE)+
facet_wrap(vars(Alue), scales = "free")+ 
theme(axis.text.x = element_text(angle=45)) 
```

<img src="kuvat\kuva5.png">

10. Toinen vaihtoehto olisi käyttää priorisointia ja estää akselin otsikoiden päällekäisyys

```
ggplot(riista , aes(Kuukausi, Riistaonnettomuudet, fill=Osallinen.eläinlaji)) +    geom_bar(stat = "identity")+
facet_wrap(vars(Alue), scales = "free")+ 
theme(strip.text.x=element_text()) +
scale_x_discrete(guide = guide_axis(check.overlap = TRUE))
```

<img src="kuvat\kuva6.png">

11. Pysytellään kuitenkin kääntämisessä, aikasemmassa esimerkissä akselin otsikot menivät kuvion päälle, joten joten siirretään niitä hjust-optiolla alaspäin 

```
ggplot(riista , aes(Kuukausi, Riistaonnettomuudet, fill=Osallinen.eläinlaji)) +    geom_bar(stat = "identity")+
facet_wrap(vars(Alue), scales = "free")+
theme(  axis.text.x = element_blank()) +
theme(axis.text.x = element_text(angle=45)) 
```

<img src="kuvat\kuva7.png">

12. Tähän asti on käytetty oletusväriskaalaa. asetetaan kuvioon Tilastokeskuksen väriskaalan mukaiset värit ja otetaan ne käyttöön scale_fill_manual -optiolla

```
rhg_cols <- c("#0073B0", "#ea7404", "#a40084", "#8a8a9e", "#253081", 
              "#049de3", "#722ea5")

ggplot(riista , aes(Kuukausi, Riistaonnettomuudet, fill=Osallinen.eläinlaji)) +    geom_bar(stat = "identity")+
facet_wrap(vars(Alue), scales = "free")+ 
theme(  axis.text.x = element_blank()) +
theme(axis.text.x = element_text(angle=45))+
scale_fill_manual(values = rhg_cols)

```

<img src="kuvat\kuva8.png">

13. Havainnot ovat nyt aakkosjärjestyksessä eläinlajin mukaan, asetetaan ne suuruusjärjestykseen manuaalisesti

```
ggplot(riista , aes(Kuukausi, Riistaonnettomuudet, fill=factor(Osallinen.eläinlaji,levels=c("Valkohäntäpeura", "Metsäkauris", "Hirvi", "Metsäpeura", "Villisika", "Kuusipeura")))) +    geom_bar(stat = "identity")+
facet_wrap(vars(Alue), scales = "free_y") +
theme(  axis.text.x = element_blank()) +
theme(axis.text.x = element_text(angle=45))+
scale_fill_manual(values = rhg_cols)
```
<img src="kuvat\kuva9.png">

14. Viimeistellään kuvio korjaamalla selitteen otsikko, asettamalla x- ja y akseleille otsikot ja antamalla koko kuviolla otsikko

```
kuva<-ggplot(riista , aes(Kuukausi, Riistaonnettomuudet, fill=factor(Osallinen.eläinlaji,levels=c("Valkohäntäpeura", "Metsäkauris", "Hirvi", "Metsäpeura", "Villisika", "Kuusipeura")))) +    geom_bar(stat = "identity")+
facet_wrap(vars(Alue), scales = "free") + theme_light() 
kuva + 
theme(  axis.text.x = element_blank()) +
theme(axis.text.x = element_text(angle=45))+
scale_fill_manual(values = rhg_cols)+
guides(fill=guide_legend(title="Eläinlaji"))+
ggtitle("Riistaonnettomuudet maakunnitain vuonna 2020") +
xlab("Kuukausi") + 
ylab("Onnettomuuksien lukumäärä")+
theme(plot.title = element_text(size=16,hjust = 0.5))
```

<img src="kuvat\kuva10.png">

```
