# pauls-game
#coding game
import pygame, sys, time, random, math
from pygame.locals import *

#TO DO
#DONEneed to have a way of determining the direction the bullets should Fire
#DONENeed to add multiple bullets
#DONEcould make bullet lists
#bullet cool downs
#DONEbetter bullets (circles)
#load sprites https://www.pygame.org/docs/ref/sprite.html
#draw sprites
#Add sounds (Dani)

width = 800
height = 600
maxSpeed = 5
pos = [200, 200]
dir = [1, 0]  #right
# x = 200
# y = 200
speed = [0, 0]
# xs = 0
# ys = 0
bulletPos = []
bulletSpeed = []
enemyPos = []
enemySpeed = []
psize = [20, 20]
# xb = -200
# yb = -200
extraBit = 10
isTouched = False
startTime = 0
sleepTime = 0 #amount of time to sleep to keep 
frameRate = 1000//60


def check_keys():
    global pos, dir, speed, bulletPos, bulletSpeed, psize, enemyPos, enemySpeed, height, width, isTouching, frameRate
    for event in pygame.event.get():

        if event.type == QUIT:
            pygame.quit()
            sys.exit()
        elif event.type == pygame.KEYUP:
            if event.key == pygame.K_LEFT:
                speed[0] = 0
            if event.key == pygame.K_RIGHT:
                speed[0] = 0
            if event.key == pygame.K_UP:
                speed[1] = 0
            if event.key == pygame.K_DOWN:
                speed[1] = 0

        elif event.type == pygame.KEYDOWN:
            if event.key == pygame.K_LEFT:
                dir = [-1, 0]
                speed[0] = (-maxSpeed )
            if event.key == pygame.K_RIGHT:
                dir = [1, 0]
                speed[0] = (maxSpeed )
            if event.key == pygame.K_UP:
                dir = [0, -1]
                speed[1] = (-maxSpeed )

            if event.key == pygame.K_DOWN:
                dir = [0, 1]
                speed[1] = (maxSpeed )

            # https://www.pygame.org/docs/ref/key.html
            if event.key == pygame.K_z:
                bulletPos.append(pos.copy())
                bulletSpeed.append(dir.copy())

            if event.key == pygame.K_x:
                ePos = [
                    random.randint(30, width - 30),
                    random.randint(30, height - 30)
                ]
                enemyPos.append(ePos.copy())
                enemySpeed.append(dir.copy())


def dist(pos1, pos2):
    dx = pos1[0] - pos2[0]
    dy = pos1[1] - pos2[1]
    return (dx * dx + dy * dy)**0.5


def isTouching(pos1, pos2, siz1, siz2):
    return dist(pos1, pos2) < siz1 + siz2


def main():
    pygame.init()
    global pos, speed, bulletPos, bulletSpeed, psize, enemyPos, enemySpeed, width, height, isTouched, bPos
    DISPLAY = pygame.display.set_mode((width, height), 0, 8)

    WHITE = (255, 255, 255)
    BLUE = (0, 0, 255)
    BLACK = (0, 0, 0)

    DISPLAY.fill(WHITE)

    pygame.draw.rect(DISPLAY, BLUE, (200, 150, psize[0], psize[1]))

    while True:
        startTime = pygame.time.get_ticks()
       # print(startTime)
        # clear the last format
        DISPLAY.fill(WHITE)
        # process the keys
        check_keys()

        # update the position
        pos[0] += speed[0]
        pos[1] += speed[1]

        #FIX THIS TO MAKE LOOK LESS BROKEN
        if (pos[0] - psize[0] * 0.5 > width - psize[0] * 0.5):
            pos[0] = psize[0]
        elif (pos[0] - psize[0] * 0.5 < psize[0] * 0.5):
            pos[0] = width - psize[0]
        if (pos[1] - psize[0] * 0.5 > height - psize[1] * 0.5):
            pos[1] = psize[1]
        elif (pos[1] - psize[1] * 0.5 < psize[1] * 0.5):
            pos[1] = height - psize[1]

        # update the bullets
        bCount = len(bulletPos)
        i = 0
        while (i < bCount):
        
            #update bullets
            bPos = bulletPos[i]
            bSpeed = bulletSpeed[i]
            # update pos
            bPos[0] += bSpeed[0]
            bPos[1] += bSpeed[1]

            #for i in range(len(enemyPos)):
            #ePos = enemyPos[i]
            #eSpeed = enemySpeed[i]
            #ePos[0] += eSpeed[0]
            #ePos[1] += eSpeed[1]

            # check new position for
            # hits

            # off screen
            if (bPos[0] > width or bPos[0] < 0 or bPos[1] > height
                    or bPos[1] < 0):
                bulletPos.remove(bPos)
                bulletSpeed.remove(bSpeed)
                bCount -= 1
                continue
            
                # might as well draw the bullets now
            pygame.draw.ellipse(DISPLAY, BLACK, (bPos[0], bPos[1], 4, 4))
            #else (bPos[0],bPos[1] == enemyPos[0],enemyPos[1]):
            i+=1

# update the enemies
 # update the bullets
        eCount = len(enemyPos)
        i = 0
        while (i < eCount):
            isTouched = False
            ePos = enemyPos[i]
            eSpeed = enemySpeed[i]

            # update pos
           # ePos[0] += eSpeed[0]
           # ePos[1] += eSpeed[1]

            # check new position for
            # hits
            if (isTouching(ePos, pos, 15, 15)):
                print("isTouched" + str(isTouched))
                isTouched = True
            if (isTouched == True):
              enemyPos.remove(ePos)
              enemySpeed.remove(eSpeed)
              eCount -= 1
              continue
            
            # off screen
            #
            if (ePos[0] > width or ePos[0] < 0 or ePos[1] > height
                    or ePos[1] < 0):
                enemyPos.remove(ePos)
                enemySpeed.remove(eSpeed)
                i -= 1
                continue
            
                # might as well draw the bullets now
            pygame.draw.ellipse(DISPLAY, BLACK, (ePos[0], ePos[1], 30, 30))
            i+=1
        
        if (isTouched):
          pCol = BLUE
        else:
          pCol = BLUE
         
        pygame.draw.rect(DISPLAY, pCol,
                         (pos[0] - psize[0] * 0.5, pos[1] - psize[1] * 0.5,
                          psize[0], psize[1]))

        pygame.display.update()
        sleepTime = frameRate -(pygame.time.get_ticks()- startTime)
        print(sleepTime)
        pygame.time.delay(int(sleepTime))

main()
