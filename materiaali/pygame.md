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

Tämän kaltaisia objekteja, voi mallintaa esimerkiksi seuraavan laisella `GameObject`-luokalla:

```python
class GameObject:
    def __init__(self, x, y, width, height):
        self.x = x
        self.y = y
        self.width = width
        self.height = height

    def move(self, dx=0, dy=0):
        self.x = self.x + dx
        self.y = self.y + dy

    def intersects(self, game_object):
        horizontal_intersection = self.x >= game_object.x and self.x < game_object.x + game_object.width
        vertical_intersection = self.y >= game_object.y and self.y < game_object.y + game_object.height

        return horizontal_intersection and vertical_intersection
```

`GameObject`-olio mallintaa siis suorakulmiota. Objektia voi liikuttaa kutsumalla `move`-metodia ja `intersects`-metodia voi kutsua tarkastaakseen leikkaako objekti jotain toista objektia.

Sokoban-pelissä erilaisia objekteja ovat:

- _Robotti_, joka toimii pelihahmona. Pelihahmo voi liikkua lattialla ja siirtää laatikoita, jos ne eivät törmää seinään tai toiseen laatikkoon
- _Seinä_, joka rajaa peli kentän. Mikään muu objekti ei voi mennä seinän läpi
- _Lattia_, jonka päällä robotti voi kävellä
- _Laatikko_, jota robotti voi siirtää, jos se ei törmää seinään tai muihin laatikoihin
- _Kohde_, johon laatikko pitäisi siirtää

Kaikkia näitä objekteja mallinnetaan suorakulmioina, joten niille voi antaa `GameObject`-olion konstruktorin kautta. Esimerkiksi `Robot`-luokassa se tapahtuisi seuraavasti:

```python
class Robot:
    def __init__(self, game_object):
        self.game_object = game_object
```

Huomaa, että `Robot`-oliolla voisi hyvin olla myös muita attribuutteja. Vaihtoehtoisesti `Robot`-luokka voisi periä `GameObject`-luokan, mutta lähtökohtaisesti kannattaa suosia [luokkien yhdistelemistä perinnän sijaan](https://en.wikipedia.org/wiki/Composition_over_inheritance).

## Pelin tilan hallinta

Objektien tilan ylläpitäminen, esimerkiksi niiden siirtely, on yksi sovelluslogiikan olennaisimmista ominaisuuksista. Tähän käyttöturkeen sopisi Sokoban-pelissä esimerkiksi luokka nimeltä `Level`:

```python
from game_object import GameObject
from robot import Robot
from box import Box
from floor import Floor
from target import Target
from wall import Wall


class Level:
    def __init__(self, level_map):
        self.robot = None
        self.walls = []
        self.targets = []
        self.floors = []
        self.boxes = []

        self._initialize_game_objects(level_map)

    def _initialize_game_objects(self, level_map):
        height = len(level_map)
        width = len(level_map[0])

        for y in range(height):
            for x in range(width):
                square = level_map[y][x]
                game_object = GameObject(x, y, 1, 1)

                if square == 0:
                    self.floors.append(Floor(GameObject(x, y, 1, 1)))
                elif square == 1:
                    self.walls.append(Wall(GameObject(x, y, 1, 1)))
                elif square == 2:
                    self.targets.append(Target(GameObject(x, y, 1, 1)))
                elif square == 3:
                    self.boxes.append(Box(GameObject(x, y, 1, 1)))
                    self.floors.append(Floor(GameObject(x, y, 1, 1)))
                elif square == 4:
                    self.robot = Robot(GameObject(x, y, 1, 1))
                    self.floors.append(Floor(GameObject(x, y, 1, 1)))
```

`Level`-luokan tarkoitus tässä vaiheessa on muodostaa kaksiulotteisesta taulukosta pelin objekteja. Luokan olion voi muodostaa esimerkiksi seuraavasti:

```python
from level import Level


def main():
    level_map = [[1, 1, 1, 1, 1],
                 [1, 0, 0, 0, 1],
                 [1, 2, 3, 4, 1],
                 [1, 1, 1, 1, 1]]

    level = Level(level_map)


if __name__ == "__main__":
    main()
```

Huomaa, ettei `Level`-luokka tiedä mistä kartan tiedot haetaan, joten melko pienellä vaivalla sen pystyisi lukemaan esimerkiksi CSV-tiedostosta. Lisäksi monimutkaisemmissa peleissä kartan esityismuoto on luultavasti monimutkaisempi. Esimerkiksi lukujen 0-9 sijaan käytössä voisi olla merkkijonoja, jotka kuvaavat minkälainen objekti ruudussa on.

Monimutkaistetaan sovelluslogiikkaa niin, että `Level`-luokka tarjoaa metodin `move_robot` robotin liikuttamiseen:

```python
def move_robot(self, dx=0, dy=0):
    self.robot.game_object.move(dx, dy)
```

## Sovelluslogiikan testaaminen

Koodia on syntynyt jo sen verran, että alkaa olla aika tehdä sovelluslogiikalle ensimmäinen. Toteutetaan testi, joka varmistaa, että robotti voi liikkua lattialla:

```python
import unittest

from level import Level

LEVEL_MAP_1 = [[1, 1, 1, 1, 1],
               [1, 0, 0, 0, 1],
               [1, 2, 3, 4, 1],
               [1, 1, 1, 1, 1]]


class TestLevel(unittest.TestCase):
    def setUp(self):
        self.level_1 = Level(LEVEL_MAP_1)

    def assert_coordinates_equal(self, game_object, x, y):
        self.assertEqual(game_object.x, x)
        self.assertEqual(game_object.y, y)

    def test_can_move_in_floor(self):
        robot = self.level_1.robot

        self.assert_coordinates_equal(robot.game_object, 3, 2)

        self.level_1.move_robot(dy=-1)
        self.assert_coordinates_equal(robot.game_object, 3, 1)

        self.level_1.move_robot(dx=-1)
        self.assert_coordinates_equal(robot.game_object, 2, 1)
```

Seuraavaksi voisi olla mielekästä toteuttaa `Level`-luokan `move_robot`-metodiin tarkastus, ettei robotti pysty kulkemaan seinien läpi. Tällä toiminallisuudella voisi jälleen toteuttaa testin, jonka jälkeen voisi siirtymään toteuttamaan sovelluslogiikkaan seuraavaa toiminallisuutta.

## Sovelluslogiikan 

Kun sovelluslogiikan toteutus on edennyt hieman pidemmälle voi siirtyä jo miettimään, miten pelin objekteja piirtäisi näytölle. Perusideana on, että käyttöliittymään liittyvän koodin tarkoituksena on ainoastaan esittää pelin tila visuaalisesti, eikä tehdä sovelluslogiikkaan liittyviä toimintoja, kuten objektien liikuttamista.

Käyttöliittymää varten voisi toteuttaa esimerkiksi luokan nimeltä `PygameRenderer`:

```python
import pygame
import os

dirname = os.path.dirname(__file__)


def load_image(filename):
    return pygame.image.load(
        os.path.join(dirname, 'assets', filename)
    )


class PygameRenderer:
    def __init__(self, display, scale, level):
        self._display = display
        self._scale = scale
        self._level = level
        self._images = {}
        self._load_images()

    def render_display(self):
        self._display.fill((0, 0, 0))

        for wall in self._level.walls:
            self._render_wall(wall)

        for floor in self._level.floors:
            self._render_floor(floor)

        for target in self._level.targets:
            self._render_target(target)

        for box in self._level.boxes:
            self._render_box(box)

        self._render_robot()

        pygame.display.flip()

    def _load_images(self):
        self._images = {
            "floor": load_image("floor.png"),
            "wall": load_image("wall.png"),
            "target": load_image("target.png"),
            "robot": load_image("robot.png"),
            "box": load_image("box.png"),
            "robot_in_target": load_image("robot_in_target.png"),
            "box_in_target": load_image("box_in_target.png"),
        }

    def _render_game_object(self, game_object, image_name):
        self._display.blit(
            self._images[image_name],
            (
                game_object.x * self._scale,
                game_object.y * self._scale,
            )
        )

    def _render_wall(self, wall):
        self._render_game_object(wall.game_object, "wall")

    def _render_floor(self, floor):
        self._render_game_object(floor.game_object, "floor")

    def _render_target(self, target):
        self._render_game_object(target.game_object, "target")

    def _render_robot(self):
        targets = self._level.targets
        robot = self._level.robot

        intersecting_targets = [
            target for target in targets if robot.game_object.intersects(target.game_object)
        ]

        if len(intersecting_targets) > 0:
            self._render_game_object(
                robot.game_object,
                "robot_in_target"
            )
        else:
            self._render_game_object(robot.game_object, "robot")

    def _render_box(self, box):
        targets = self._level.targets

        intersecting_targets = [
            target for target in targets if box.game_object.intersects(target.game_object)
        ]

        if len(intersecting_targets) > 0:
            self._render_game_object(box.game_object, "box_in_target")
        else:
            self._render_game_object(box.game_object, "box")
```

`PygameRenderer`-luokka see konstruktorin kautta `Level`-olion ja muita tarvittavia tietoja, joita se tarvitsee objektien piirtämiseen. Huomaa, ettei luokassa ole raskasta sovelluslogiikkaa, vaan sen metodit vain piirtävät näytölle kuvia `Level`-olion tietojen perusteella. Luokkaa voi käyttää seuraavasti:

```python
from level import Level


def main():
    # kuvien koko pikseleissä
    scale = 50
    level_map = [[1, 1, 1, 1, 1],
                 [1, 0, 0, 0, 1],
                 [1, 2, 3, 4, 1],
                 [1, 1, 1, 1, 1]]

    height = len(level_map)
    width = len(level_map[0])
    display_height = height * scale
    display_width = width * scale
    display = pygame.display.set_mode((display_width, display_height))

    pygame.display.set_caption("Sokoban")

    level = Level(level_map)
    renderer = PygameRenderer(display, scale, level)


if __name__ == "__main__":
    main()
```

## Pelaajan syötteet

