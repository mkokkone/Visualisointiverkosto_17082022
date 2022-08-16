# Visualisointiverkosto 17.8.2022

Tälle sivulle on koottu esimerkki teemakartan teosta [riistaonnettomuudet-tilaston](https://www.stat.fi/tup/kokeelliset-tilastot/riistaonnettomuudet/index.html) tilastoaineistolla


## Teemakartta Qgis-karttaohjelmalla

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


```
