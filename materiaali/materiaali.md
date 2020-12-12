<!-- TODO -->
# Ohjelmistotekniikka syksy 2020

Keväästä 2018 alkaen Ohjelmistotekniikka (vanhalta nimeltään Ohjelmistotekniikan menetelmät) on siirtynyt aineopintoihin. Kurssin esitietoina on Ohjelmoinnin jatkokurssi sekä Tietokantojen perusteet. Oletuksena on, että molemmista kursseista on käyty suhteellisen tuore versio ja että molempien aihepiiri on vielä hyvin mielessä.

Kurssin oppimistavoitteet ovat edelleen suunnilleen samat kuin aiemmin. Kurssin suoritettuaan opiskelija

- Tuntee ohjelmistotuotantoprosessin vaiheet
- On tietoinen vesiputousmallin ja ketterän ohjelmistotuotannon luonteesta
- Osaa soveltaa versionhallintaa osana ohjelmistokehitystä
- Osaa soveltaa UML-mallinnustekniikkaa ohjelmiston suunnittelussa ja dokumentoinnissa tarkoituksenmukaisella tavalla
- Tuntee ohjelmiston testauksen eri vaiheet
- Osaa soveltaa automatisoitua testausta yksinkertaisissa ohjelmistoprojekteissa
- Tuntee tärkeimpiä ohjelmiston suunnitteluperiaatteita ja osaa soveltaa niitä yksinkertaisissa projekteissa

Kurssin suoritusmuoto poikkeaa radikaalisti aiemmasta viikoittaiset luennot ja laskuharjoitukset sisältävästä kurssista, nykyinen OTM muistuttaakin läheisesti entistä Ohjelmoinnin harjoitustyötä.

Kurssin ensimmäisen kolmen viikon aikana harjoitellaan versionhallintaa, yksikkötestausta sekä UML-kaavioiden tekemistä. Toisesta viikosta alkaen aloitetaan oman harjoitustyön tekeminen. Harjoitustyön tekemisen ohessa osoitetaan riittävä osaaminen kurssin oppimistavoitteiden suhteen, koetta kurssilla ei ole. Tarkemmat arvosteluperusteet [täällä](./arvosteluperusteet.md).

Tälle sivulle on koottu erinäistä asiaa liittyen kurssin "teoriaan" sekä erinäisiin menetelmiin, kuten UML-kaavioihin. Sivu kannattaa lukea kokonaisuudessaan heti ensimmäisen viikon aikana, tosin luvusta [Työkaluja](https://github.com/mluukkai/ohjelmistotekniikka-syksy-2020/blob/main/web/materiaali.md#työkaluja) alkaen olevaa asiaa tarvitaan oikeastaan vasta viikosta 2 eteenpäin.

Ohjeita työn aloittamiseen [täällä](https://github.com/mluukkai/ohjelmistotekniikka-syksy-2020/blob/main/web/tyon_aloitus.md)

# Kirjoitusvirheitä

Jos huomaat tehtävissä tai muussa materiaalissa kirjoitusvirheitä, kirjaudu GitHubiin ja toimi [täällä](./typokorjaukset.md) olevan ohjeen mukaan.

# Ohjelmistotuotanto

Kun tehdään pientä ohjelmaa omaan käyttöön, ei työskentelymenetelmillä ole suurta merkitystä. Kun ohjelmiston koko on suurempi ja erityisesti, jos sitä tehdään useamman ihmisen toimesta ulkoiselle käyttäjälle tai tilaajalle, ei pelkkä häkkeröinti enää tuota optimaalista tulosta. Tarvitaankin jonkinlainen systemaattinen menetelmä ohjaamaan ohjelmistokehittäjien toimintaa ja varmistamaan, että ohjelmistosta tulee käyttäjien käyttötarkoitukseen sopiva.

Ohjelmiston systemaattinen tekeminen, eli ohjelmistotuotanto (engl. Software engineering) sisältää useita erilaisia aktiviteettejä, joiden aikana tekemisen fokus on hieman erilaisissa asioissa. Näitä aktiviteetteja tai _vaiheita_ niinkuin niitä joskus nimitetään ovat seuraavat

- _vaatimusmäärittely_ jonka tehtävä on selvittää, kuinka ohjelmiston halutaan toimivan
- _suunnittelu_ jonka aikana mietitään, miten halutunkaltainen ohjelmisto tulisi rakentaa
- _toteutusvaiheessa_ määritelty ja suunniteltu ohjelmisto koodataan
- _testauksen_ tehtävä taas on varmistaa ohjelmiston laatu, että se ei ole liian buginen ja että se toimii kuten vaatimusmäärittely sanoo
- _ylläpitovaiheessa_ ohjelmisto on jo käytössä ja siihen tehdään bugikorjauksia ja mahdollisia laajennuksia.

Katsotaan vielä kutakin vaihetta hieman tarkemmin.

Käytetään seuraavassa esimerkkinä kurssia varten tehtyä yksinkertaista [todo](https://github.com/mluukkai/OtmTodoApp)-sovellusta.

## Vaatimusmäärittely

Vaatimusmäärittelyn aikana kartoitetaan ohjelman tulevien käyttäjien tai tilaajan kanssa se, mitä toiminnallisuutta ohjelmaan halutaan. Ohjelman toiminnalle siis asetetaan asiakkaan haluamat vaatimukset. Tämän lisäksi kartoitetaan ohjelman toimintaympäristön ja toteutusteknologian järjestelmälle asettamia rajoitteita.

Vaatimusmäärittelyn tuloksena on useimmiten jonkinlainen dokumentti, johon vaatimukset kirjataan. Dokumentin muoto vaihtelee suuresti, se voi olla paksu mapillinen papereita tai vaikkapa joukko postit-lappuja.

### Todo-sovelluksen vaatimusmäärittely

Esimerkkisovelluksemme on siis klassinen _TodoApp_, eli sovellus, jonka avulla käyttäjien on mahdollista pitää kirjaa omista tekemättömistä töistä, eli _todoista_.

Vaatimusmäärittely kannattaa yleensä aloittaa tunnistamalla järjestelmän erityyppiset _käyttäjäroolit_. Sovelluksellamme ei ole toistaiseksi muuta kuin normaaleja käyttäjiä. Jatkossa sovellukseen saatetaan lisätä myös ylläpitäjän oikeuksilla varustettu käyttäjärooli.

Kun sovelluksen käyttäjäroolit ovat selvillä, mietitään mitä toiminnallisuuksia kunkin käyttäjäroolin halutaan pystyvän tekemään sovelluksen avulla.

Todo-sovelluksen normaalien käyttäjien toiminnallisuuksia ovat esim. seuraavat

- käyttäjä voi luoda järjestelmään käyttäjätunnuksen
- käyttäjä voi kirjautua järjestelmään
- kirjautumisen jälkeen käyttäjä näkee omat tekemättömät työt eli todot
- käyttäjä voi luoda uuden todon
- käyttäjä voi merkitä todon tehdyksi, jolloin todo häviää listalta

Ylläpitäjän toiminnallisuuksia voisivat olla esim. seuraavat

- ylläpitäjä näkee tilastoja sovelluksen käytöstä
- ylläpitäjä voi poistaa normaalin käyttäjätunnuksen

Ohjelmiston vaatimuksiin kuuluvat myös _toimintaympäristön rajoitteet_. Todo-sovellusta koskevat seuraavat rajoitteet

- ohjelmiston tulee toimia Linux- ja OSX-käyttöjärjestelmillä varustetuissa koneissa
- käyttäjien ja todojen tiedot talletetaan paikallisen koneen levylle

Vaatimusmäärittelyn aikana hahmotellaan yleensä myös sovelluksen käyttöliittymä.

Kurssin aiemmissa versioissa käyttäjien vaatimukset dokumentointiin [käyttötapauksina](https://en.wikipedia.org/wiki/Use_case) (engl. use case). Käytämme tällä kurssilla hieman kevyempää tapaa, ja kirjaamme järjestelmältä toivotun toiminnallisuuden vapaamuotoisena ranskalaisista viivoista koostuvana feature-listana. Katso tarkemmin Todo-sovelluksen alustavasta [vaatimusmäärittelystä](https://github.com/mluukkai/OtmTodoApp/blob/master/dokumentaatio/vaatimusmaarittely.md).

## Suunnittelu

Ohjelmiston suunnittelu jakautuu yleensä kahteen erilliseen vaiheeseen.

_Arkkitehtuurisuunnittelussa_ määritellään ohjelman rakenne karkealla tasolla

- mistä suuremmista rakennekomponenteista ohjelma koostuu
- miten komponentit yhdistetään, eli minkälaisia komponenttien väliset rajapinnat ovat
- mitä riippuvuuksia ohjelmalla on esim. tietokantoihin ja ulkoisiin rajapintoihin

Arkkitehtuurisuunnittelua tarkentaa _oliosuunnittelu_, missä mietitään ohjelmiston yksittäisten komponenttien rakennetta, eli minkälaisista luokista komponentit koostuvat ja miten luokat kutsuvat toistensa metodeja sekä mitä apukirjastoja luokat käyttävät.

Myös ohjelmiston suunnittelu, erityisesti sen arkkitehtuuri dokumentoidaan usein jollain tavalla. Joskus tosin dokumentaatio on hyvin kevyt, esim. valkotaululle piirretty kaavio tai se voi jopa puuttua kokonaan ja ajatellaankin että hyvin muotoiltu koodi voi korvata dokumentoinnin.

## Testaus

Toteutuksen yhteydessä ja sen jälkeen järjestelmää testataan. Testauksessa on monta erilaista näkökulmaa, eli pääasiallista kiinnostuksen kohdetta. Näitä eri näkökulmia nimitetään usein _testaustasoiksi_. Testauksen terminologia vaihtelee hieman mutta yleisimmin puhutaan kolmesta testaustasosta eli yksikkötestauksesta, integraatiotestauksesta ja järjestelmätestauksesta.

_Yksikkötestauksessa_ (engl. unit testing) tutkitaan yksittäisten metodien ja luokkien toimintaa. Yksikkötestauksen tekee usein testattavan luokan ohjelmoija ja hyvä tapa on tehdä luokalle yksikkötestit samalla kun luokka ohjelmoidaan.

Kun erikseen ohjelmoidut komponentit (eli luokat tai luokkien muodostamat kokoelmat) yhdistetään, suoritetaan integraatiotestaus (engl. integration testing), jossa varmistetaan erillisten komponenttien yhteentoimivuus. Myös integraatiotestit tehdään useimmiten ohjelmoijien toimesta.

_Järjestelmätestauksessa_ (engl. system testing) testataan järjestelmää kokonaisuutena ja verrataan, että se toimii vaatimusdokumentissa sovitun määritelmän mukaisesti. Järjestelmätestauksessa testien näkökulma on sama kuin loppukäyttäjän, eli testit suoritetaan ohjelmiston käyttöliittymän kautta. Järjestelmätestauksen suorittavat usein testauksen ammattilaiset.

## Vesiputousmalli

Ohjelmistoja on perinteisesti tehty vaihe vaiheelta etenevän _vesiputousmallin_ (engl. waterfall model) mukaan. Vesiputousmallissa edellä esitellyt ohjelmistotuotannon vaiheet suoritetaan peräkkäin:

![](https://raw.githubusercontent.com/mluukkai/ohjelmistotekniikka-syksy-2020/main/web/images/l-1.png)

Vesiputousmallissa suoritetaan siis ensin vaatimusmäärittely, jonka seurauksena kirjoitetaan vaatimusdokumentti, johon pyritään kokoamaan kaikki ohjelmalle osoitettavat vaatimukset mahdollisimman tarkasti dokumentoituna. Määrittelyvaiheen päätteeksi vaatimusdokumentti jäädytetään. Jäädytettyä vaatimusmäärittelyä käytetään usein ohjelman kehittämisen vaatimien resurssien arvioinnin perustana ja myös sopimus ohjelman hinnasta saatetaan tehdä vaatimusmäärittelyn pohjalta.

Vaatimusmäärittelyä seuraa suunnitteluvaihe, joka myös dokumentoidaan tarkoin. Pääsääntöisesti suunnitteluvaiheen aikana ei enää tehdä muutoksia määrittelyyn. Joskus tämäkin on tarpeen. Suunnittelu pyritään tekemään niin täydellisenä, että ohjelmointivaiheessa ei enää ole tarvetta muuttaa suunnitelmia.

Suunnittelun jälkeen toteutetaan ohjelman yksittäiset komponentit ja tehdään niille yksikkötestaus. Tämän jälkeen erilliset komponentit liitetään yhteen eli integroidaan ja suoritetaan integrointitestaus.

Integroinnin jälkeen ohjelmalle tehdään järjestelmätestaus, eli testataan, että ohjelmisto toimii kokonaisuutena niin kuin määrittelydokumentissa on määritelty.

Vesiputousmalli on monella tapaa ongelmallinen. Mallin toimivuus perustuu siihen oletukseen, että ohjelman vaatimukset pystytään määrittelemään täydellisesti ennen kuin suunnittelu ja ohjelmointi alkaa. Näin ei useinkaan ole. On lähes mahdotonta, että asiakkaat pystyisivät tyhjentävästi ilmaisemaan kaikki ohjelmalle asettamansa vaatimukset. Vähintäänkin riski sille, että ohjelma on käytettävyydeltään huono, on erittäin suuri. Usein käy myös niin, että vaikka ohjelman vaatimukset olisivat kunnossa vaatimusten laatimishetkellä, muuttuu toimintaympäristö (tapahtuu esim. yritysfuusio) ohjelman kehitysaikana niin ratkaisevasti, että valmistuessaan ohjelma on vanhentunut. Hyvin yleistä on myös se, että vasta käyttäessään valmista ohjelmaa asiakkaat alkavat ymmärtää, mitä he olisivat ohjelmalta halunneet.

Asiakkaan muuttuvien vaatimuksien lisäksi toinen suuri ongelma on se, että vesiputousmallissa ohjelmistoa aletaan testata verrattain myöhäisessä vaiheessa. Erityisesti integraatiotestauksessa on tyypillistä että ohjelmasta löydetään pahoja ongelmia, joiden korjaaminen hidastaa ohjelmiston valmistumista paljon ja käy kalliiksi.

## Ketterä ohjelmistokehitys

Vesiputousmallin heikkoudet ovat johtaneet viime vuosina yleistyneiden _ketterien (engl. agile) ohjelmistokehitysmenetelmien_ käyttöönottoon.

Ketterissä menetelmissä lähdetään oletuksesta, että vaatimuksia ei voi tyhjentävästi määritellä ohjelmistokehitysprosessin alussa. Koska näin ei voida tehdä, ei sitä edes yritetä vaan pyritään toimimaan niin, että asiakkaan vaatimukset saadaan tarkennettua pikkuhiljaa ohjelmiston kehitysprosessin aikana ja lopputuloksesta saadaan sitä kautta mahdollisimman halutun kaltainen.

Ketterä ohjelmistokehitys etenee yleensä siten, että ensin kartoitetaan pääpiirteissään ohjelman vaatimuksia ja ehkä hahmotellaan järjestelmän alustava arkkitehtuuri. Tämän jälkeen suoritetaan useita _iteraatioita_ (joista käytetään yleisesti myös nimitystä sprintti), joiden aikana ohjelmaa rakennetaan pala palalta eteenpäin. Kussakin iteraatiossa suunnitellaan ja toteutetaan valmiiksi pieni osa ohjelman vaatimuksista. Vaatimukset voivat myös tarkentua koko prosessin ajan.

Yksittäinen iteraatio, joka on kestoltaan tyypillisesti 1-4 viikkoa, siis lisää järjestelmään pienen osan koko järjestelmän toivotusta toiminnallisuudesta. Tyypillisesti tärkeimmät ja toteutuksen kannalta haasteellisimmat ja riskialttiimmat toiminnallisuudet toteutetaan ensimmäisillä iteraatioilla. Yksi iteraatio sisältää toteutettavaksi valittujen vaatimusten tarkennuksen, suunnittelun, toteutuksen sekä testauksen.

Jokainen iteraatio tuottaa toimivan ja toteutettujen ominaisuuksien kannalta testatun järjestelmän. Asiakas pääsee kokeilemaan järjestelmää jokaisen iteraation jälkeen. Tällöin voidaan jo aikaisessa vaiheessa todeta, onko kehitystyö etenemässä oikeaan suuntaan ja vaatimuksia voidaan tarvittaessa tarkentaa ja lisätä.

Jokainen iteraatio siis sisältää määrittelyä, suunnittelua, ohjelmointia ja testausta ja jokaisen iteraation jälkeen saadaan asiakkaalta palautetta siitä, onko kehitystyö etenemässä oikeaan suuntaan:

<img src="https://raw.githubusercontent.com/mluukkai/ohjelmistotekniikka-syksy-2020/main/web/images/l-2.png" width="700">

Ketterässä ohjelmistokehityksessä dokumentointi ei ole yleensä niin keskeisessä osassa kuin perinteisissä menetelmissä.

Vähäisemmän dokumentaation sijaan testauksella ja ns. jatkuvalla integroinnilla on hyvin suuri merkitys. Yleensä pyritään siihen, että järjestelmään lisättävät uudet komponentit testataan välittömästi ja pyritään heti integroimaan kokonaisuuteen; tästä työskentelytavasta käytetään nimitystä _jatkuva integrointi_ (engl. continuous integration). Näin uusia versioita järjestelmästä syntyy jopa päivittäin.

Uusien komponenttien toimiminen pyritään varmistamaan perinpohjaisella automaattisella testauksella. Joskus jopa "testataan ensin", eli jo ennen uuden komponentin toteuttamista ohjelmoidaan komponentin toimintaa testaavat testitapaukset. Testitapausten valmistuttua toteutetaan komponentti ja siinä vaiheessa kun komponentti läpäisee testitapaukset, se integroidaan muuhun kokonaisuuteen.

Erilaisia ketteriä ohjelmistokehitysmenetelmiä on olemassa lukuisia, näistä tunnetuin nykyään on Scrum.

Ketterät menetelmät ovat nykyään vallitseva tapa tehdä ohjelmistoja. Ketterien menetelmien rinnalle ovat viime vuosina nousseet ketteryyden ideaa hieman jalostavat Lean-menetelmät. Palaamme aiheeseen tarkemmin kurssilla Ohjelmistotuotanto.

Tämän kurssin harjoitustyö pyritään tekemään osittain ketterien menetelmien hengessä, eli vaatimusmäärittely ja suunnittelu pidetään kevyenä ja ohjelmaa aletaan toteuttaa jo heti alkuvaiheessa. Ohjelmasta pyritään mahdollisuuksien mukaan tekemään jokaisen iteraation eli viikon päätteeksi toimiva versio jota sitten viikko viikolta laajennetaan. Kurssin vaatimaa dokumentaatiota tehdään osin matkan varrella.

# Työkaluja

Tarvitsemme ohjelmisokehityksessä suuren joukon käytännön työkaluja.

## Komentorivi ja versionhallinta

Olet jo ehkä käyttänyt muilla kursseilla komentoriviä ja versionhallintaa, molemmat ovat tärkeässä roolissa ohjelmistokehityksessä ja niiden harjoittelu on aiheena viikon 1 [tehtävissä](../tehtavat/viikko1.md).

## Poetry

Olet todennäköisesti ohjelmoinut Pythonia tähän asti ilman ulkoisia ulkoisten riippuvuuksien asennusta ja käyttöä. Alamme tämän kurssin myötä hieman tutkimaan, miten Pythonilla tehdyn ohjelmiston riippuvuuksien hallinta tapahtuu ja minkälaisia ovat siihen työskentelytavat.

Python-projektien riippuvuuksien hallintaan on olemassa muutamiakin vaihtoehtoja, joista _pip_ saattaa olla jo ennestään tuttu. Kurssilla tutustumme [Poetry](https://python-poetry.org/-työkaluun, jolla on paljon samankaltaisuuksia pipin kanssa ja helpottaa työskentelyämme huomattavasti. Ohje Poetry käytön aloittamiseen löytyy [täältä](./poetry.md).

## Unittest

Ohjelmistojen testaus tapahtuu nykyään ainakin yksikkö- ja integraatiotestien osalta automatisoitujen testityökalujen toimesta. Python-maailmassa testien toteuttamisessa on muodostonut jo standardiksi [unittest](https://docs.python.org/3/library/unittest.html)-moduulin käyttö. Tulet kurssin ja myöhempienkin opintojesi aikana kirjoittamaan paljon unittest-testejä.

Unittestiin tutustumme viikon 2 [tehtävissä](../tehtavat/viikko2.md).

## Docstring

Osa ohjelmiston dokumentointia on lähdekoodin API:n eli käytännössä luokkien, metodien ja fuktioiden kuvaamista. Pythonissa lähdekoodi dokumentoidaan käyttäen [docstring](https://google.github.io/styleguide/pyguide.html#381-docstrings)-kommentteja. Dokumentointi tapahtuu kirjoittamalla koodin yhteyteen sopivasti muotoiltuja kommentteja.

Visual Studio Code näyttää ohjelmoidessa koodiin määritellyn docstringin seuraavasti:

![Docstring](./kuvat/docstring.png)

Ohje docstring-kommenttien käyttöön löytyy [täältä](./docstring.md)

## Pylint

Automaattisten testien lisäksi koodille voidaan määritellä erilaisia automaattisesti tarkastettavia tyylillisiä sääntöjä, joiden avulla on mahdollista ylläpitää koodin luettavuutta ja varmistaa että joka puolella koodia noudatetaan samoja tyylillisiä konventioita.

Käytämme kurssilla tarkoitukseen [pylint](https://www.pylint.org/)-nimistä työkalua:

> Pylint is a tool that checks for errors in Python code, tries to enforce a coding standard and looks for code smells. It can also look for certain type errors, it can recommend suggestions about how particular blocks can be refactored and can offer you details about the code's complexity.

Ohje pylintin käyttöön löytyy [täältä](./pylint.md).

## UML

Ohjelmistojen dokumentoinnissa ja sovelluksen suunnittelun yhteydessä on usein tapana visualisoida ohjelman rakennetta ja toimintaa [UML](https://en.wikipedia.org/wiki/Unified_Modeling_Language)-kaavioilla.

UML tarjoaa lukuisia erilaisia kaaviotyyppejä, hyödynnämme kurssilla kuitenkin näistä ainoastaan kolmea.

### Luokkakaaviot

Kurssilla [Tietokantojen perusteet](https://tikape-k20.mooc.fi) olet saattanut jo tutustua luokkakaavioiden käyttöön. Luokkakaavioiden käyttötarkoitus on ohjelman luokkien ja niiden välisten suhteiden kuvailu. Todo-sovelluksen oleellista tietosisältöä edustavat käyttäjää vastaava luokka `User`:

```python
class User:
    def __init__(self, username, password):
        self.username = username
        self.password = password
```

ja tehtävää vastaava luokka `Todo`:

```python
import uuid

class Todo:
    def __init__(self, content, done=False, user=None, todo_id=None):
        self.content = content
        self.done = done
        self.user = user
        self.id = todo_id or str(uuid.uuid4())

    def set_done(self):
        self.done = True
```

Jokaiseen todoon liittyy yksi käyttäjä, ja yksittäiseen käyttäjään liittyviä todoja voi olla useita. Tilannetta kuvaa seuraava luokkakaavio

![Luokkakaavio](./kuvat/materiaali-luokkakaavio-1.png)

Luokkakaavioon on nyt merkitty molempien luokkien oliomuuttujat sekä metodit.

Yleensä ei ole mielekästä kuvata luokkia tällä tarkkuudella, eli luokkakaavioihin riittää merkitä luokan nimi

<img src="https://raw.githubusercontent.com/mluukkai/ohjelmistotekniikka-syksy-2020/main/web/images/l-4.png" width="350">

Luokkien tarkemmat detaljit selviävät koodia katsomalla tai docstring-dokumentoinnista.

#### Riippuvuus

UML-kaavioissa olevat "viivat" kuvaavat luokkien olioiden välistä _pysyvää yhteyttä_. Joissain tilanteissa on mielekästä merkata kaavioihin myös ei-pysyvää suhdetta kuvaava katkoviiva, eli _riippuvuus_.

Eräs tällainen tilanne voisi olla Unicafe-ruokalan kassapäätteen toiminallisuudesta vastaava koodi. Koodissa on kaksi luokkaa `Maksukortti` ja `Kassapaate`, joiden välillä ei ole pysyvää yhteyttä.

Maksukortin koodi on seuraava:

```python
class Maksukortti:
  def __init__(self, saldo):
      self.saldo = saldo

  def lataa_rahaa(self, lisays):
      self.saldo += lisays
  
  def ota_rahaa(self, maara):
      if self.saldo < maara:
          return False

      self.saldo -= maara

      return True
```

Kuten huomataan, koodissa ei mainita kassapäätettä millään tavalla.

Kassapäätteen hieman lyhennetty koodi on seuraava:

```python
EDULLISEN_HINTA = 2.5
MAUKKAAN_HINTA = 4.3

class Kassapaate:
    def __init__():
        self.edulliset = 0
        self.maukkaat = 0

    def syo_edullisesti(self, kortti):
        if kortti.saldo() < EDULLISEN_HINTA:
            return False

        kortti.ota_rahaa(EDULLISEN_HINTA);
        self.edulliset += 1
        return True

    def syo_maukkaasti(self, kortti):
        # ...
    
    def lataa_rahaa_korttille(self, kortti, summa):
        if summa < 0:
            return

        kortti.lataa_rahaa(summa)
        self.rahaa += summa
```

Kassapääte käyttää maksukortteja hetkellisesti lounaiden maksamisen ja rahan lataamisen yhteydessä. Kassapääte ei kuitenkaan muista pysyvästi yksittäisiä maksukortteja. Tämän takia kassapäätteellä on riippuvuus maksukortteihin, mutta ei kuitenkaan normaalia yhteyttä, sillä UML-kaavioon merkattu yhteys viittaa pysyvään, ajallisesti pidempikestoiseen suhteeseen.

Tilannetta kuvaava luokkakaavio on seuraava:

<img src="https://raw.githubusercontent.com/mluukkai/ohjelmistotekniikka-syksy-2020/main/web/images/l-18.png" width="450">

Riippuvuus siis kuvataan _katkoviivallisena nuolena_, joka kohdistuu siihen luokkaan mistä ollaan riippuvaisia. Riippuvuuteen ei merkitä numeroa toisin kuin yhteyteen.

Tarkastellaan toisena esimerkkinä riippuvuudesta todo-sovelluksen sovelluslogiikasta vastaavaa luokkaa `TodoService`, jonka koodi hieman lyhennettynä näyttää seuraavalta:

```python
class TodoService:
    def __init__(self, todo_repository, user_repository):
        self._user = None
        self._todo_repository = todo_repository
        self._user_repository = user_repository

    def create_todo(self, content):
        todo = Todo(content=content, user=self._user)

        return self._todo_repository.create(todo)

    def get_undone_todos(self):
        if not self._user:
            return []

        todos = self._todo_repository.find_by_username(self._user.username)
        undone_todos = filter(lambda todo: not todo.done, todos)

        return list(undone_todos)

    # ...
```

Sovelluslogiikkaa hoitava olio tuntee kirjautuneen käyttäjän, mutta pääsee käsiksi kirjautuneen käyttäjän todoihin ainoastaan `todo_repository`-olion välityksellä. Tämän takia luokalla ei ole yhteyttä luokkaan `Todo`, luokkien välillä on kuitenkin _riippuvuus_, sillä sovelluslogiikka käsittelee metodeissaan todo-olioita.

Merkitään luokkakaavioon seuraavasti:

![Luokkakaavio](./kuvat/materiaali-luokkakaavio-2.png)

Riippuvuuksien merkitseminen luokkakaavioihin ei ole välttämättä kovin oleellinen asia, niitä kannattaa merkitä jos ne tuovat esille tilanteen kannalta jotain oleellista.

#### Perintä

Luokkien [perintähierarkian](https://docs.python.org/3/tutorial/classes.html#inheritance) ilmaisemisessa käytetään nuolia, joissa on valkoiset päät. Esim. jos Todo-sovelluksessa olisi normaalin käyttäjän eli luokan `User` perivää ylläpitäjää kuvaava luokka `SuperUser`, merkattaisiin se luokkakaavioon seuraavasti:

<img src="https://raw.githubusercontent.com/mluukkai/ohjelmistotekniikka-syksy-2020/main/web/images/l-9.png" width="350">

### Pakkauskaavio

Todo-sovelluksen koodi on sijoitettu hakemistoihin seuraavasti:

![](./kuvat/materiaali-hakemistorakenne.png)

Hakemistorakennetta voidaan kuvata UML:ssä _pakkauskaaviolla_:

![](./kuvat/materiaali-pakkaukset.png)

Pakkausten välille on merkitty _riippuvuudet_ katkoviivalla. Pakkaus _ui_ riippuu pakkauksesta _services_ sillä _ui_-pakkauksen luokat käyttävät _services_-pakkauksen luokkaa `TodoService`, joka vastaa sovelluksen sovelluslogiikasta. 

Vastaavasti pakkaus _services_ riippuu pakkauksesta _repositories_ sillä sen luokka `TodoService` käyttää _repositorios_-pakkauksen luokkia `TodoRepository` ja `UserRepository`.

Pakkauskaavioihin on myös mahdollista merkitä pakkausten sisältönä olevia luokkia normaalin luokkakaaviosyntaksin mukaan:

![](./kuvat/materiaali-pakkaukset-luokat.png)

Sovelluksen koodi on organisoitu _kerrosarkkitehtuurin_ periaatteiden mukaan. Asiasta lisää hieman myöhemmin tässä dokumentissa.

### Sekvenssikaaviot

Luokka- ja pakkauskaaviot kuvaavat ohjelman rakennetta. Ohjelman toiminta ei kuitenkaan tule niistä ilmi millään tavalla.

Esimerkiksi Unicafe-ruokalan maksukortin ja kassapäätteen välistä suhdetta kuvaava luokkakaavio voisi näyttää seuraavalta:

<img src="https://raw.githubusercontent.com/mluukkai/ohjelmistotekniikka-syksy-2020/main/web/images/l-15.png" width="550">

Vaikka kaavioon on merkitty metodien nimet, ei ohjelman toimintalogiikka, esimerkiksi mitä tapahtuu kun kortilla ostetaan edullinen lounas, selviä kaaviosta millään tavalla.

_Sekvenssikaaviot_ on alunperin kehitetty kuvaamaan verkossa olevien ohjelmien keskinäisen kommunikoinnin etenemistä. Sekvenssikaaviot sopivat kohtuullisen hyvin kuvaamaan myös sitä, miten ohjelman oliot kutsuvat toistensa metodeja suorituksen aikana.

Koodia katsomalla näemme, että lounaan maksaminen tapahtuu siten, että ensin kassapääte kysyy kortin saldoa ja jos se on riittävä, vähentää kassapääte lounaan hinnan kortilta ja palauttaa _True_:

```python
EDULLISEN_HINTA = 2.5

class Kassapaate:
    # ...

    def syo_edullisesti(self, kortti):
        if kortti.saldo < EDULLISEN_HINTA:
            return False
        
        kortti.ota_rahaa(EDULLISEN_HINTA)
        self.edulliset += 1
        return True
    
    # ...
```

Sekvenssikaaviona kuvattuna tilanne näyttää seuraavalta:

<img src="https://raw.githubusercontent.com/mluukkai/ohjelmistotekniikka-syksy-2020/main/web/images/l-16.png" width="450">

Sekvenssikaaviossa oliot kuvataan laatikoina, joista lähtee alaspäin olion "elämänlanka". Kaaviossa aika etenee ylhäältä alas. Metodikutsut kuvataan nuolena, joka yhdistää kutsujan ja kutsutun olion elämänlangat. Paluuarvo merkitään katkoviivalla. Attribuuttin arvon lukeminen tai asettaminen voidaan kuvata kaaviossa metodikutsun tavoin. Tästä esimerkkinä kaavion `saldo`-attribuutin lukeminen.

Jos saldo ei riitä, etenee suoritus seuraavan sekvenssikaavion tapaan:

<img src="https://raw.githubusercontent.com/mluukkai/ohjelmistotekniikka-syksy-2020/main/web/images/l-17.png" width="450">

Tarkastellaan hieman monimutkaisempaa tapausta, yrityksen palkanhallinnasta vastaavaa ohjelmaa:

```python
class Henkilo:
    def __init__(self, nimi, palkka, tilinumero):
        self.nimi = nimi
        self.palkka = palkka
        self.tilinumero = tilinumero

class Henkilostorekisteri:
    def __init__(self):
        self._henkilot = {}
        self._pankki = PankkiRajapinta()
    
    def lisaa(self, henkilo):
        self._henkilot[henkilo.nimi] = henkilo
    
    def suorita_palkanmaksu(self):
        for nimi in self._henkilot:
            henkilo = self._henkilot[nimi]
            self._pankki.maksa_palkka(henkilo.tilinumero, henkilo.palkka)
    
    def aseta_palkka(self, nimi, uusi_palkka):
        henkilo = self._henkilot[nimi]
        henkilo.palkka = uusi_palkka

class PankkiRajapinta:
    # ...

    def maksa_palkka(tilinumero, summa):
        # suorittaa maksun verkkopankin internet-rajapinnan avulla
        # yksityiskohdat piilotettu
```

Sekvenssikaaviot siis kuvaavat yksittäisten suoritusskenaarioiden aikana tapahtuvia asioita. Kuvataan nyt seuraavan pääohjelman aikaansaamat tapahtumat:

```python
def main():
    rekisteri = Henkilostorekisteri()

    arto = Henkilo("Hellas", 1200, "1234-12345")
    rekisteri.lisaa(arto)

    sasu = Henkilo("Tarkoma", 6500, "4455-123123")
    rekisteri.lisaa(sasu)

    rekisteri.aseta_palkka("Hellas", 3500)

    rekisteri.suorita_palkanmaksu()
```

Sekvenssikaavio on seuraavassa:

![](./kuvat/materiaali-sekvenssikaavio-rekisteri.png)

Kaavio alkaa tilanteesta, jossa `Henkilostorekisteri`-luokan olio on jo luotu, mutta henkilöolioita ei vielä ole olemassa.

Toiminta alkaa siitä, kun pääohjelma eli main luo henkilön nimeltä `arto`. Seuraavaksi `main`-fuktiosta kutsutaan rekisterin metodia `lisaa`, jolle annetaan argumentiksi luotu henkilöolio. Vastaava toistuu kun `main` luo uuden henkilön ja lisää sen rekisteriin.

Seuraavana toimenpiteenä main kasvattaa arton palkkaa kutsumalla rekisterin metodia `aseta_palkka`. Tämä saa aikaan sen, että `rekisteri` asettaa `arto`-olion `palkka`-attribuutille uuden arvon. Rekisterin viivaan on merkitty paksunnus, joka korostaa, että attribuutille on asetettu arvo. Huomaa, että olion attribuutin asettamista voidaan kuvata metodikutsun tavoin.

Viimeinen ja monimutkaisin toiminnoista käynnistyy, kun `main` kutsuu rekisterin metodia `suorita_palkanmaksu`. Rekisteri kysyy ensin arton tilinumeroa ja palkkaa ja kutsuu paluuarvoina olevilla tiedoilla pankin metodia `maksa_palkka` ja sama toistuu `sasu`-olion kohdalla.

Sekvenssikaaviot eivät ole optimaalinen tapa ohjelman suorituslogiikan kuvaamiseen. Ne sopivat jossain määrin olio-ohjelmien toiminnan kuvaamiseen, mutta esim. funktionaalisella tyylillä tehtyjen ohjelmien kuvaamisessa ne ovat varsin heikkoja.

Tietynlaisten tilanteiden kuvaamiseen ohjelmoinnin perusteissakin käsitellyt [vuokaaviot](https://materiaalit.github.io/ohjelmointi-18/part2/) voivat sopia paremmin.

Voit halutessasi lukea lisää sekvenssikaavioista kurssin vanhan version [materiaalista](https://github.com/mluukkai/OTM2016/blob/master/luennot/luento5.pdf).

# Lisää ohjelmiston suunnittelusta

Katsotaan seuraavassa muutamia sovelluksen suunnittelussa noudatettuja periaatteita.

## Kerrosarkkitehtuuri

Kuten jo mainittiin, todo-sovellus noudattaa kerrosarkkitehtuuria. Koodin tasolla kerrosrakenne näkyy siinä, miten sovelluksen koodi jakautuu hakemistoihin:

![](./kuvat/materiaali-hakemistorakenne.png)

Ja minkälaisia riippuvuuksia hakemistojen välisillä luokilla on. Riippuvuudet kuvaava pakkauskaavio havainnollistaa koodin rakenteen kerroksellisuuden:

![](./kuvat/materiaali-pakkaukset-luokat.png)

Kerrosarkkitehtuurissa ylimpänä on _käyttöliittymästä_ vastaava kerros. Käyttöliittymäkerroksen vastuulla on muodostaa sovelluksen käyttöliittymä ja reagoida käyttäjän syötteisiin.

Sovelluslogiikka, eli esim. käyttäjän kirjautumisesta huolehtiminen, todojen luominen ja niiden tehdyksi merkkaaminen on käyttöliittymän alapuolella olevan _sovelluslogiikkakerroksen_ vastuulla. Sovelluslogiikkakerroksen koodi on hakemistossa nimeltään _services_.

Sovelluslogiikan alapuolella on _datan tallennuksesta vastaava kerros_, jonka käytännössä muodostavat [Repository](https://docs.microsoft.com/en-us/dotnet/architecture/microservices/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design)-suunnittelumallin inspiroimana toteutetut luokat `TodoRepository` ja `UserRepository`.

[Kerrosarkkitehtuuri](https://en.wikipedia.org/wiki/Multitier_architecture) (engl. layered architecture tai multitier architecture) on ehkä eniten käytetty ohjelmistojen [arkkitehtuurimalli](https://en.wikipedia.org/wiki/Software_Architecture_styles_and_patterns), eli yleisesti käytetty tapa ohjelmiston rakenteen strukturointiin. Käytännössä lähes jokainen ohjelmisto noudattaa ainakin jossain määrin kerroksellisuuden periaatetta. On olemassa lukuisia arkkitehtuurimalleja, joihin tutustutaan tarkemmin kursseilla Ohjelmistotuotanto ja Ohjelmistoarkkitehtuurit.

## Hyvän ohjelmiston periaatteita

Ohjelmistojen suunnitteluun on aikojen saatossa muodostunut joukko periaatteita, joiden noudattamisen on todettu parantavan koodin laatua.

### DRY eli Don't repeat yourself

Jo Ohjelmoinnin perusteissa aloittelevaa ohjelmoijaa varoitellaan copy-pasten vaaroista. [DRY](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself)-periaate ilmaisee asian seuraavasti

> Every piece of knowledge must have a single, unambiguous, authoritative representation within a system

Periaate yleistää toisteettomuuden koskemaan koodin lisäksi muitakin ohjelmistoon liittyviä asioita, esim. dokumentaatiota. Luokkien dokumentoiminen docstring-kommenttien avulla ilmentää osin tätä periaatetta.

### Single responsibility principle

_Single responsibility_ tarkoittaa karkeasti ottaen, että _luokan olioilla ja funktioilla tulee olla vain yksi vastuu_ eli yksi asiakokonaisuus, mihin liittyvästä toiminnasta luokan oliot tai funktiot itse huolehtivat. Tämän jo vuosikymmeniä vanhan säännön nimen lanseerannut Robert "Uncle Bob" Martin ilmaisee asian seuraavasti _A class should have only one reason to change_.

Kerrosarkkitehtuurin voi ajatella ilmentävän tätä periaatetta laajentaen sen yksittäisten luokkien ja olioiden tasolta sovellusten suurempiin kokonaisuuksiin.

Todo-sovelluksen suunnittelussa periaatetta on noudatettu suhteellisen hyvin

- Käyttöliittymästä on eristetty sovelluslogiikka kokonaan
- Käyttäjä ja tehtävät on talletettu omiin luokkiinsa `User` ja `Todo`
- Sovelluslogiikan suorittamisesta, eli `User`- ja `Todo`-olioiden manipuloinnista vastaa oma luokka `TodoService`
- Tietojen talletuksesta levylle vastaavat repositorio-oliot, jotka on vielä jaettu kahteen vastuualueeseen eli käyttäjistä vastaavaan `UserRepository`- ja todoista vastaavaan `TodoRepository`-luokkaan.

### Riippuvuuksien minimointi

Minimoi riippuvuudet, eli älä tee _spagettikoodia_, jossa kaikki sovelluksen komponentit tuntevat toisensa. Pyri eliminoimaan riippuvuudet siten, että esimerkiksi luokat tuntevat mahdollisimman vähän muita luokkia. Riippuvuuksien määrän huomaa helposti katsomalla tiedoston `import`-rivejä.

Kerrosarkkitehtuuri tähtää osaltaan riippuvuuksien eliminointiin, esim. käyttöliittymä on nyt riippuvainen ainoastaan sovelluslogiikkakerroksen luokista `TodoService` ja `Todo`, mutta ei millään tavalla tietojen talletuksesta vastaavista repositorio-luokista.

## Riippuvuuksien injektointi

Turhien riippuvuuksien eliminointiin liittyy läheisesti tapa, jolla oliot pääsevät käsiksi riippuvuuksiinsa eli tarvitsemiinsa olioihin.

Sovelluslogiikasta huolehtiva `TodoService`-olio tarvitsee toimiakseen `TodoRepository`- ja `UserRepository`-oliot. Se voi tarvittaessa ottaa oliot konstruktorin parametrina:

```python
class TodoService:
    def __init__(self, todo_repository, user_repository):
        self._todo_repository = todo_repository
        self._user_repository = user_repository
```

Parametreille voi antaa myös oletusarvot, jolloin ne voi määritellä vain halutessaan:

```python
from repositories.todo_repository import (
    todo_repository as default_todo_repository
)

from repositories.user_repository import (
    user_repository as default_user_repository
)

class TodoService:
    def __init__(
        self,
        todo_repository=default_todo_repository,
        user_repository=default_user_repository
    ):
        self._todo_repository = todo_repository
        self._user_repository = user_repository
```

Riippuvuuksien injektointi onnistuu luokkien lisäksi myös esimerkiksi funktioilla:

```python
def calculate_sum(get_input=input):
    a = get_input()
    b = get_input()

    return int(a) + int(b)

calculate_sum()

inputs = ["1", "2"]

def fake_get_input():
    return inputs.pop(0)

calculate_sum(fake_get_input)
```

Tekniikasta, missä oliolle annetaan sen riippuvuudet ulkopuolelta joko konstruktorin parametrina, erillisten metodien avulla tai jollain muulla tekniikalla, käytetään nimitystä _riippuvuuksien injektointi_ (engl. [dependency injection](http://www.jamesshore.com/Blog/Dependency-Injection-Demystified.html)).

## Riippuvuuksien injektointi ja testaus

Riippuvuuksien injektointi helpottaa erityisesti testaamista, sillä se mahdollistaa, että luokille annetaan niiden normaalien riippuvuuksien sijaan testausta varten luotuja _valekomponentteja_.

Todo-sovelluksessa on luokkaa `TodoService` testattu juuri näin. Esim. `UserRepository`-luokan valekomponentti pitää käyttäjät muistissa:

```python
class FakeUserRepository:
    def __init__(self, users=None):
        self._users = users or []

    def find_all(self):
        return self._users
    
    # ...
```

Repositorioiden valekomponentit injektoidaan testattavalle luokalle:

```python
class TestTodoService(unittest.TestCase):
    def setUp(self):
        self.todo_service = TodoService(
            FakeTodoRepository(),
            FakeUserRepository()
        )

        self.todo_a = Todo('testing a')
        self.todo_b = Todo('testing b')
        self.user_kalle = User('kalle', 'kalle123')

    def login_user(self, user):
        self.todo_service.create_user(user.username, user.password)
        self.todo_service.login(user.username, user.password)

    # ...

    def test_login_with_valid_username_and_password(self):
        self.login_user(self.user_kalle)

    def test_login_with_invalid_username_and_password(self):
        self.assertRaises(
            InvalidCredentials,
            lambda: self.todo_service.login('testing', 'invalid')
        )
    
    # ...
```

Toisin kuin todelliset repositoriot, testeissä käytettävät valekomponentit eivät tallenna dataa levylle. Tämä tekee testaamisesta helpompaa.

Katso lisää Todo-sovelluksen [arkkitehtuurikuvauksesta](https://github.com/ohjelmistotekniikka-hy/python-todo-app/blob/master/dokumentaatio/arkkitehtuuri.md) ja [testausdokumentista](https://github.com/ohjelmistotekniikka-hy/python-todo-app/blob/master/dokumentaatio/testaus.md).

## Ohjelmiston toteutus

Muutamia käyttöliittymän ja tietojen tallettamisen toteuttamiseen sekä sovelluksen konfigurointiin liittyviä vihjeitä on koottu [tänne](./python.md)
