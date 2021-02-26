# Vinkkejä pelien toteutukseen

Pelien toteutus [Pygame](https://www.pygame.org)-kirjastolla saattaa olla jo tuttua Ohjelmoinnin jatkokurssilta. Kuten jo moneen otteeseen on mainittu, sovelluslogiikan ja käyttöliittymän erottaminen toisistaan muodostuu erittäin tärkeäksi ohjelmiston testattavuuden ja laajennettavuuden kannalta. Etenkin pelien kohdalla on helppo sortua ajatukseen, että kehitystä kannattaa tehdä käyttöliittymä edellä. Tämä johtaa helposti spagetti-koodiin, jossa käyttöliittymän ja sovelluslogiikan koodi nivoutuvat tiiviisti yhteen siten, että koodin luettavuus ja testattavuus kärsivät.

Tutustutaan tässä osiossa vinkkeihin, miten pelien toteutusta voisi lähestyä. Esimerkkinä osiossa käytetään yksinkertaista [Sokoban](https://fi.wikipedia.org/wiki/Sokoban)-peliä, jota on käytetty esimerkkinä myös mm. Ohjelmoinnin jatkokurssilla. Projektin lähdekoodi löytyy [tästä](https://github.com/ohjelmistotekniikka-hy/pygame-sokoban) repositoriosta.

## Pygame-kirjaston asennus Poetryn avulla

Pygamen asennus Poetry-projektissa onnistuu seuraavasti:

```shell
poetry install pygame
```

## Sovelluslogiikan suunnitteleminen

Pelin sovelluslogiikan suunnittelussa kannattaa lähteä liikkeellä miettimillä, mitä asioita peliin liittyy. Yleensä peleissä käsitellään paljon objekteja, joita voidaan mallintaa kaksiulotteisissa peleissä esimerkiksi suorakulmioilla, joilla on x- ja y-koordinaatit, sekä leveys- ja korkeus-arvo. Moni pelien sovellesloogiikkaan liittyvä toiminallisuus liittyy siihen, mitkä pelin objektit leikkaavat toisiaan, eli selkokielellä "törmäävät" toisiinsa. Tästä hyvänä esimerkkinä se, ettei pelihahmo yleensä pysty kävelemään seinän läpi.

Sokoban-pelissä erilaisia objekteja ovat:

- _Robotti_, joka toimii pelihahmona. Pelihahmo voi liikkua lattialla ja siirtää laatikoita, jos ne eivät törmää seinään tai toiseen laatikkoon
- _Seinä_, joka rajaa peli kentän. Mikään muu objekti ei voi liikkua seinän läpi
- _Lattia_, jonka päällä robotti voi kävellä
- _Laatikko_, jota robotti voi siirtää, jos se ei törmää seinään tai muihin laatikoihin
- _Kohde_, johon laatikko pitäisi siirtää

Pygamessa pelin objekteja mallinnetaan usein luokilla, jotka perivät [Sprite](https://www.pygame.org/docs/ref/sprite.html#pygame.sprite.Sprite)-luokan. Peliohjelmoinnin yhteydessä [sprite](<https://en.wikipedia.org/wiki/Sprite_(computer_graphics)>)-termillä tarkoitetaan kaksiulotteisia kuvia, joita käytetään usein antamaan pelin objekteille niiden visuaaliainen ilme. `Sprite`-luokka tarjoaa visuaalisen ilmeen lisäksi myös muita hyödyllisiä toiminallisuuksia, kuten mahdollisuuden tarkistaa törmääkö tietty `Sprite`-olio joidenkin muiden `Sprite`-olioiden kanssa.

Sokoban-pelissä pelattavan hahmon `Robot`-luokka näyttää seuraavalta:

```python
import pygame
import os

# polku tämän tiedoston hakemistoon
dirname = os.path.dirname(__file__)

# Peritään Sprite-luokka
class Robot(pygame.sprite.Sprite):
    def __init__(self, x=0, y=0):
        # kutsutaan yläluokan konstruktoria
        super().__init__()

        # muodostetaan polku tiedoston hakemistosta hakemistoon, jossa kuva sijaitsee
        self.image = pygame.image.load(
            os.path.join(dirname, 'assets', 'robot.png')
        )

        # määritellään objektin ulottuvuudet. Tässä tapauksessa muodostetaan suorakulmia kuvan koon perusteella (50x50)
        self.rect = self.image.get_rect()

        # asetetaan alustavat x- ja y-koordinaatin arvot konstruktorin argumenttien perusteella
        self.rect.x = x
        self.rect.y = y
```

Luokan `image`-attribuutin arvoksi tulee asettaa kuva, joka piirretään näytölle. Attribuutti `rect` määrittää objektin ulottuvuudet suorakulmiona. Attribuutin arvo on helpointa asettaa kuvan ulottuvuuksien perusteella kutsumalla sen `get_rect`-metodia. Suorakulmion x- ja y-koordinaatin arvot kannattaa asettaa luokan konstruktorin argumenttien perusteella.

## Pelin tilan hallinta

Pelin objektien tilan hallinta, kuten tieto, missä koordinaateissa objektit sijaitsevat, on yksi sovelluslogiikan tärkeimmistä vastuualueista. Kun sovelluslogiikka on toteuttu, on pelinäkymän piirtäminen kohtalaisen triviaali vaihe. Sokoban-pelissä yksittäisen tason tilan hallinnasta vastaa `Level`-luokka:

```python
fimport pygame
from robot import Robot
from box import Box
from floor import Floor
from target import Target
from wall import Wall


class Level:
    def __init__(self, level_map, grid_size):
        self.grid_size = grid_size
        self.robot = None
        self.walls = pygame.sprite.Group()
        self.targets = pygame.sprite.Group()
        self.floors = pygame.sprite.Group()
        self.boxes = pygame.sprite.Group()
        self.all_sprites = pygame.sprite.Group()

        self._initialize_sprites(level_map)

    def _initialize_sprites(self, level_map):
        height = len(level_map)
        width = len(level_map[0])

        for y in range(height):
            for x in range(width):
                square = level_map[y][x]
                normalized_x = x * self.grid_size
                normalized_y = y * self.grid_size

                if square == 0:
                    self.floors.add(Floor(normalized_x, normalized_y))
                elif square == 1:
                    self.walls.add(Wall(normalized_x, normalized_y))
                elif square == 2:
                    self.targets.add(Target(normalized_x, normalized_y))
                elif square == 3:
                    self.boxes.add(Box(normalized_x, normalized_y))
                    self.floors.add(Floor(normalized_x, normalized_y))
                elif square == 4:
                    self.robot = Robot(normalized_x, normalized_y)
                    self.floors.add(Floor(normalized_x, normalized_y))

        self.all_sprites.add(
            self.floors,
            self.walls,
            self.targets,
            self.boxes,
            self.robot
        )
```

`Level`-luokan tarkoitus tässä vaiheessa on muodostaa kaksiulotteisesta taulukosta pelin objekteja. Luokan olion voi muodostaa esimerkiksi seuraavasti:

```python
from level import Level

LEVEL_MAP = [[1, 1, 1, 1, 1],
             [1, 0, 0, 0, 1],
             [1, 2, 3, 4, 1],
             [1, 1, 1, 1, 1]]

GRID_SIZE = 50


def main():
    level = Level(LEVEL_MAP, GRID_SIZE)


if __name__ == "__main__":
    main()
```

Luokan konstruktorin `grid_size`-argumentti kuvastaa pelin kuvien kokoa. Kun kuvien koko on 50 pikseliä, tulee esimerkiksi taulukon indeksissä `[1][2]` oleva ruutu piirtää `(x, y)`-pisteeseen `(100, 50)`.

Pelin spritet kannttaa jaotella ryhmiin, jotka lisätään [Group](https://www.pygame.org/docs/ref/sprite.html#pygame.sprite.Group)-luokan olioon. Esimerkiksi seinät on lisätty `initialize_sprites`-metodissa `walls`-attribuuttiin tallennettuun `Group`-olioon kutsumalla olion `add`-metodia seuraavasti:

```python
self.walls.add(Wall(normalized_x, normalized_y))
```

Spritejen ryhmittelyn on suuria etuja. Esimerkiksi kaikki ryhmän spritet pystyy helposti piirtämään yhdellä `draw`-metodin kutsulla ja ryhmän spritejen törmäyksen tarkastaminen jonkin toisen spriten kanssa onnistuu yhdellä [spritecollide](https://www.pygame.org/docs/ref/sprite.html#pygame.sprite.spritecollide)-funktion kutsulla.

Kaikista pelin spriteistä kannattaa koostaa yksi ryhmä, joka helpottaa niiden piirtämistä. Edellisessä esimerkissä kaikki spritet tallennettiin `all_sprites`-attribuutiin:

```python
self.all_sprites.add(
    self.floors,
    self.walls,
    self.targets,
    self.boxes,
    self.robot
)
```

Kuten näkyy, `Group`-olioon voi lisätä `add`-metodin avulla helposti yksittäisen spriten, tai kaikki spritet jostain tietystä ryhmästä. Huomaa, että _lisäysjärjestyksellä on väliä_, sillä ensimmäisenä ryhmään lisätty sprite piirretään ensimmäisenä. Jos esimerkiksi `floors` lisättäisiin ryhmään viimeisenä, piirtyisi samoissa koordinaateissa oleva laatikko tai robotti lattian alle.

Voimme nyt piirtää `Level`-olion kaikki spritet seuraavasti:

```python
import pygame
from level import Level

LEVEL_MAP = [[1, 1, 1, 1, 1],
             [1, 0, 0, 0, 1],
             [1, 2, 3, 4, 1],
             [1, 1, 1, 1, 1]]

GRID_SIZE = 50


def main():
    height = len(LEVEL_MAP)
    width = len(LEVEL_MAP[0])
    display_height = height * GRID_SIZE
    display_width = width * GRID_SIZE
    display = pygame.display.set_mode((display_width, display_height))

    pygame.display.set_caption("Sokoban")

    level = Level(level_map, GRID_SIZE)

    pygame.init()

    level.all_sprites.draw()

if __name__ == "__main__":
    main()
```

Rivillä `level.all_sprites.draw()` kutsutaan `all_sprites`-attribuuttiin tallennetun `Group`-olion metodia `draw`, joka piirtää spritet näytölle.

## Spriten siirtäminen

Spritejen siirtäminen monissa peleissä yksi tärkeimmistä toiminallisuuksista. Spriten siirtäminen onnistuu muuttamalla `Sprite`-olion `rect`-olion `x`- ja `y`-attribuuttien arvoja. Esimerkiksi robotin siirtämistä varten voi toteuttaa `Level`-luokkaan seuraavanlaisen metodin:

```python
def move_robot(self, dx=0, dy=0):
    self.robot.rect.x += dx
    self.robot.rect.y += dy
```

Robotin liikuttelu onnistuu nyt seuraavasti:

```python
# liikutaan ylös
level.move_robot(dy=-50)
# liikutaan alas
level.move_robot(dy=50)
# liikutaan oikealle
level.move_robot(dx=-50)
# liikutaan vasemmalle
level.move_robot(dx=50)
```

## Sovelluslogiikan testaaminen

Koodia on syntynyt jo sen verran, että alkaa olla aika toteuttaa sovelluslogiikalle ensimmäinen testi. Toteutetaan testi, joka varmistaa, että robotti voi liikkua lattialla:

```python
import unittest
from level import Level

LEVEL_MAP_1 = [[1, 1, 1, 1, 1],
               [1, 0, 0, 0, 1],
               [1, 2, 3, 4, 1],
               [1, 1, 1, 1, 1]]

GRID_SIZE = 50


class TestLevel(unittest.TestCase):
    def setUp(self):
        self.level_1 = Level(LEVEL_MAP_1)

    def assert_coordinates_equal(self, sprite, x, y):
        self.assertEqual(sprite.rect.x, x)
        self.assertEqual(sprite.rect.y, y)

    def test_can_move_in_floor(self):
        robot = self.level_1.robot

        self.assert_coordinates_equal(robot, 3 * GRID_SIZE, 2 * GRID_SIZE)

        self.level_1.move_robot(dy=-GRID_SIZE)
        self.assert_coordinates_equal(robot, 3 * GRID_SIZE, GRID_SIZE)

        self.level_1.move_robot(dx=-GRID_SIZE)
        self.assert_coordinates_equal(robot, 2 * GRID_SIZE, GRID_SIZE)
```

Seuraavaksi voisi olla mielekästä toteuttaa `Level`-luokan `move_robot`-metodiin tarkistus, ettei robotti pysty kulkemaan seinien läpi. Tälle toiminallisuudelle voisi jälleen toteuttaa testin. Toteutusta ja testaamista kannattaa siis tehdä lyhyissä sykleissä.

## Törmäyksien tarkastaminen

Tällä hetkellä robotti voi liikkua seinien läpi, joka on pelin logiikan vastaista. Kuten edellä mainittiin, eräs hyvä puoli spritejen ryhmittelyssä on törmäyksen tarkastaminen. Toteutetaan `Level`-luokaan luokan sisäinen metodi `robot_can_move`, joka tarkistaa, voiko robotti liikkua argumenttina annettujen arvojen verran:

```python
def _robot_can_move(self, dx=0, dy=0):
    # siirretään robotti uuteen sijaintiin
    self.robot.rect.x += dx
    self.robot.rect.y += dy

    # tarkisteaan osuuko robotti johonkin seinään
    colliding_walls = pygame.sprite.spritecollide(self.robot, self.walls, False)

    can_move = not colliding_walls

    # siirretään robotti takaisin alkuperäiseen sijaintiin
    self.robot.rect.x -= dx
    self.robot.rect.y -= dy

    return can_move
```

Metodi ensin siirtää robottia, jonka jälkeen se tarkistaa [spritecollide](https://www.pygame.org/docs/ref/sprite.html#pygame.sprite.spritecollide)-funktion avulla osuuko jokin seinistä robottiin. Funktion viimeinen argumentti, `dokill`, on boolean-arvo, joka kertoo poistetaanko kaikkii törmänneet spritet ryhmästä. Koska emme halua näin tapahtuvan, asetamme sen arvoksi `False`. Voimme hyödyntää `robot_can_move`-metodia edellä toteutetussa `move_robot`-metodissa seuraavasti:

```python
def move_robot(self, dx=0, dy=0):
    if not self._robot_can_move(dx, dy):
        return

    self.robot.rect.x += dx
    self.robot.rect.y += dy
```

## Pelaajan syötteiden lukeminen

Peli pyörii usein ikuisen loopin sisällä, josta käytetään nimitystä "Game loop". Tämän loopin sisällä luetaan pelaajan syötteet, päivitetään pelin tila syötteiden perusteella ja piirretään uusi näkymä. Loopin voi toteuttaa esimerkiksi seuraavanlaisen `GameLoop`-luokan avulla:

```python
import pygame


class GameLoop:
    def __init__(self, level, grid_size, display):
        self._level = level
        self._clock = pygame.time.Clock()
        self._grid_size = grid_size
        self._display = display

    def start(self):
        while True:
            if self._handle_events() == False:
                break

            self._render()

            # jokaista sekunttia kohden piirretään maksimissaan 60 näkymää
            self._clock.tick(60)

    def _handle_events(self):
        for event in pygame.event.get():
            if event.type == pygame.KEYDOWN:
                if event.key == pygame.K_LEFT:
                    self._level.move_robot(dx=-self._grid_size)
                if event.key == pygame.K_RIGHT:
                    self._level.move_robot(dx=self._grid_size)
                if event.key == pygame.K_UP:
                    self._level.move_robot(dy=-self._grid_size)
                if event.key == pygame.K_DOWN:
                    self._level.move_robot(dy=self._grid_size)
            elif event.type == pygame.QUIT:
                return False

    def _render(self):
        self._level.all_sprites.draw(self._display)

        pygame.display.update()
```

Luokan metodi `start` käynnistää ikuisen loopin. Loopin sisällä kutsutaan ensimmäiseksi luokan `handle_events`-metodia. Metodi lukee for-loopissa tapahtumia Pygamen tapahtumaloopista. Tapahtumien perusteella kutsutaan sovelluslogiikan metodeja. Kun tapahtumat on käsitelty, luokan `render`-metodi piirtää pelin tilan perusteella seuraavan näkymän. Loopin viimeinen rivi, `self._clock.tick(60)`, rajoittaa näkymien piirtämiseen 60 sekunnissa. Rivi on erityisen tärkeä, jos pelissä on aikaan sidottuja tapahtumia, kuten vihollisten liikkumista.

Tässä muodossa `GameLoop`-luokan testaaminen on vähintään hankalaa. Testaamista hankaloittaa riippuvuudet Pygamen tapahtumalooppiin, näytön piirtämiseen ja ajastuksiin. Onneksi olemme jo oppineet, kuinka nämä ongelmat voidaan ratkaista _riippuvuuksien injektoinnilla_. Voimme toteuttaa riippuvuuksille yksinkertaiset abstraktiot.

Pygamen tapahtumaloopin voi toteuttaa `EventLoop`-luokka:

```python
import pygame

class EventLoop:
    def get(self):
        return pygame.event.get()
```

Näkymän piirtämisestä voi vastata `Renderer`-luokka:

```python
import pygame


class Renderer:
    def __init__(self, display, level):
        self._display = display
        self._level = level

    def render(self):
        self._level.all_sprites.draw(self._display)

        pygame.display.update()
```

Ja vastuun ajastuksista voi ottaa `Clock`-luokka:

```python
import pygame

class Clock:
    def __init__(self):
        self._clock = pygame.time.Clock()

    def tick(self, fps):
        self._clock.tick(fps)
```

Nyt voimme injektoida riippuvuudet `GameLoop`-luokkaan sen konstruktorin kautta ja hyödyntää niitä luokan metodeissa:

```python
import pygame

class GameLoop:
    def __init__(self, level, renderer, event_loop, clock, grid_size):
        self._level = level
        self._renderer = renderer
        self._event_loop = event_loop
        self._clock = clock
        self._grid_size = grid_size

    def start(self):
        while True:
            if self._handle_events() == False:
                break

            self._render()

            self._clock.tick(60)

    def _handle_events(self):
        for event in self._event_loop.get():
            if event.type == pygame.KEYDOWN:
                if event.key == pygame.K_LEFT:
                    self._level.move_robot(dx=-self._grid_size)
                if event.key == pygame.K_RIGHT:
                    self._level.move_robot(dx=self._grid_size)
                if event.key == pygame.K_UP:
                    self._level.move_robot(dy=-self._grid_size)
                if event.key == pygame.K_DOWN:
                    self._level.move_robot(dy=self._grid_size)
            elif event.type == pygame.QUIT:
                return False

    def _render(self):
        self._renderer.render()
```

Luokan käyttö onnistuu seuraavasti:

```python
import pygame
from level import Level
from game_loop import GameLoop
from event_loop import EventLoop
from renderer import Renderer
from clock import Clock

LEVEL_MAP = [[1, 1, 1, 1, 1],
             [1, 0, 0, 0, 1],
             [1, 2, 3, 4, 1],
             [1, 1, 1, 1, 1]]

GRID_SIZE = 50

def main():
    height = len(LEVEL_MAP)
    width = len(LEVEL_MAP[0])
    display_height = height * GRID_SIZE
    display_width = width * GRID_SIZE
    display = pygame.display.set_mode((display_width, display_height))

    pygame.display.set_caption("Sokoban")

    level = Level(LEVEL_MAP, GRID_SIZE)
    event_loop = EventLoop()
    renderer = Renderer(display, level)
    clock = Clock()
    game_loop = GameLoop(level, renderer, event_loop, clock, GRID_SIZE)

    pygame.init()
    game_loop.start()


if __name__ == "__main__":
    main()
```

`GameLoop`-luokan testaaminen onnistuu hyödyntämällä valeluokkia:

```python
import unittest
import pygame

from level import Level
from game_loop import GameLoop


class StubClock:
    def tick(self, fps):
        pass


class StubEvent:
    def __init__(self, event_type, key):
        self.type = event_type
        self.key = key


class StubEventLoop:
    def __init__(self, events):
        self._events = events

    def get(self):
        return self._events


class StubRenderer:
    def render(self):
        pass


LEVEL_MAP_1 = [[1, 1, 1, 1, 1],
               [1, 0, 0, 0, 1],
               [1, 2, 3, 4, 1],
               [1, 1, 1, 1, 1]]

GRID_SIZE = 50


class TestGameLoop(unittest.TestCase):
    def setUp(self):
        self.level_1 = Level(LEVEL_MAP_1, GRID_SIZE)

    def test_can_complete_level(self):
        events = [
            StubEvent(pygame.KEYDOWN, pygame.K_LEFT),
        ]

        game_loop = GameLoop(
            self.level_1,
            StubRenderer(),
            StubEventLoop(events),
            StubClock(),
            GRID_SIZE
        )

        game_loop.start()

        self.assertTrue(self.level_1.is_completed())
```

Luokat `StubRenderer` ja `StubClock` eivät tee mitään, koska niiden toiminallisuudella ei ole testin kannalta merkitystä. `StubEventLoop`-luokkalle annetaan ennalta määrätty lista tapahtumia, jotka `GameLoop`-luokka käy läpi ja toteuttaa niiden mukaiset toimenpiteet. 
