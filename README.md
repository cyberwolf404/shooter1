from pygame import *
from random import randint
win_width = 1200
win_height = 800
window = display.set_mode((win_width,win_height))
display.set_caption('Шутер')
background = transform.scale(image.load('galaxy.jpg'),(win_width,win_height))



class GameSptite(sprite.Sprite):
    def __init__(self, player_image, player_x,player_y,size_z,size_y,player_speed):
        sprite.Sprite.__init__(self)
        self.image = transform.scale(image.load(player_image),(size_z,size_y))
        self.speed = player_speed
        self.rect = self.image.get_rect()
        self.rect.x = player_x
        self.rect.y = player_y

    def reset(self):
        window.blit(self.image,(self.rect.x,self.rect.y))

goal = 3
max_lost = 3
score = 0
lost = 0
class Player(GameSptite):
    def update(self):
        keys = key.get_pressed()
        if keys[K_LEFT] and self.rect.x > 5:
            self.rect.x -= self.speed
        if keys[K_RIGHT] and self.rect.x < win_width - 80:
            self.rect.x += self.speed
    def fire(self):
        bullet = Bullet('bullet.png', self.rect.centerx,self.rect.top,15,20,-15)
        bullets.add(bullet)





class Bullet(GameSptite):
    def update(self):
        self.rect.y += self.speed
        if self.rect.y < 0:
            self.kill()

font.init()
font1 = font.Font(None,80)
win = font1.render('You win!',True,(255,255,255))
lose = font1.render('You lose!',True,(180,0,0))
font2 = font.Font(None,36)

clock = time.Clock()
FPS = 60


mixer.init()
mixer.music.load('space.ogg')
mixer.music.play()

class Enemy(GameSptite):
    def update(self):
        self.rect.y += self.speed
        global lost
        if self.rect.y >win_height:
            self.rect.x = randint(80,win_width - 80)
            self.rect.y = 0
            lost = lost + 1

#font2 = font.Font(None,36)

monsters = sprite.Group()
bullets = sprite.Group()
for i in range(1,6):
    moster = Enemy('ufo.png',randint(80,win_width - 80), -40,80,50,randint(2,6))
    monsters.add(moster)

player = Player('rocket.png',5, win_height -100,80,100,15)
#monster = Enemy('cyborg.png',win_width -80,280,2)
#final = GameSptite('treasure.png',win_width - 120, win_height -80,0)
finish = False
game = True
while game:
    for e in event.get():
        if e.type == QUIT:
            game = False
        if e.type == KEYDOWN:
            if e.key == K_SPACE:
                player.fire()



#начало



#конец

    if not finish:
        monsters.update()
        window.blit(background,(0,0))
        player.reset()
        player.update()

        monsters.draw(window)
        
        bullets.update()
        
        collides = sprite.groupcollide(monsters,bullets,True,True)
        for c in collides:
            score = score + 1
            monster = Enemy('ufo.png',randint(80,win_width - 80), -40 ,80 ,50, randint(1,5))
            monsters.add(monster)

        if sprite.spritecollide(player, monsters,False) or lost >= max_lost:#создать перем
            finish = True
            window.blit(lose,(200,200)) 

        if score >= goal:
            finish = True
            window.blit(win,(200,200))
        text_lose = font2.render('Пропущено:' + str(lost),1,(255,255,255))
        window.blit(text_lose,(10,50))
        text = font2.render('счёт:' + str(score),1,(255,255,255))
        window.blit(text,(10,20))
        bullets.draw(window)
        display.update()
    time.delay(50)
    




















