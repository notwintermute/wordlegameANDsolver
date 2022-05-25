import pygame
import random
import requests

# get the list of words (thank you tabatkins)
res = requests.get('https://raw.githubusercontent.com/tabatkins/wordle-list/main/words')
dictionary = res.text.split()
res = requests.get('https://static.nytimes.com/newsgraphics/2022/01/25/wordle-solver/assets/solutions.txt')
possibleAns = res.text.split()
remainingWords = list(dictionary)
remainHold = remainingWords
compWord = possibleAns[random.randrange(len(list(possibleAns)))]
compHold = compWord
recc = ''
countLetters = []
sampleWord = 'no word yet'
# useful
alphabet = "abcdefghijklmnopqrstuvwxyz"
alphaList = list(alphabet)
letterStatus = []  # 0 - unknown, 1 - not in the word, 2 - in the word but wrong pos, 3 - in the word right pos
letterPos = []
letterHold = []
for x in range(26):
    letterStatus.append(0)
    letterPos.append([0, 0])
    letterHold.append([0, 0])
pygame.init()
# initializing the font
font = pygame.font.SysFont('calibri', 20)


# display resolution
squareRes = 600
UI = 400
borderWidth = 5
squareWidth = squareRes / 5
resX = squareRes
resY = squareRes + squareWidth
# create input rectangle
input_rect = pygame.Rect(resX, 0, UI, squareWidth)
tColor = (255, 255, 255)
active = False
updateStep = False
randomRequest = False
nextGuess = False
base_font = pygame.font.Font(None, 150)
user_text = ''
greens = ['', '', '', '', '']
# color parameters
black = [75, 75, 75]
grey = [155, 155, 155]
yellow = [180, 180, 0]
green = [0, 180, 0]
clrs = [black, grey, yellow, green]
clrsStat = [(255, 255, 255), grey, yellow, green]
# storing the letters/words
numGuesses = 0
guesses = []
guessHold = []
result = []
for x in range(5):
    result.append([])
    for y in range(6):
        result[x].append(0)
colors = []
for x in range(5):
    colors.append([])
    for y in range(6):
        colors[x].append(black)


# functions
def checkAnswer(gs):
    global compWord
    rs = []
    for x in range(5):
        rs.append([])
        for y in range(6):
            rs[x].append(0)
    for y, word in enumerate(gs):
        for x, let in enumerate(word):
            if let not in compWord:
                rs[x][y] = 1
            else:
                if let == compWord[x]:
                    rs[x][y] = 3
                else:
                    rs[x][y] = 2
    return rs


def updateLPos(rs, lp):
    pass


def clearGrey(rem, lP):
    greyLetters = []
    for index, pair in enumerate(lP):
        if pair[1] == 1:
            greyLetters.append(alphaList[index])
    if not len(greyLetters) == 0:
        res = []
        for word in rem:
            if all(chr not in word for chr in greyLetters):
                res.append(word)
        return res
    else:
        return rem


def clearYellow(rem, lP):
    yellowLetters = []
    yellowPos = []
    for index, pair in enumerate(lP):
        if pair[1] == 2:
            yellowLetters.append(alphaList[index])
            yellowPos.append(pair[0])
    if not len(yellowLetters) == 0:
        res1 = []
        for word in rem:
            if all(l in word for l in yellowLetters):
                res1.append(word)
        res2 = []
        for word in res1:
            addword = True
            for x in range(len(yellowPos)):
                if word[yellowPos[x]] == yellowLetters[x]:
                    addword = False
            if addword:
                res2.append(word)
        return res2
    else:
        return rem


def clearGreen(rem, lP):
    greenLetters = []
    greenPos = []
    addword = True
    for index, pair in enumerate(lP):
        if pair[1] == 3:
            greenLetters.append(alphaList[index])
            greenPos.append(pair[0])
    if not len(greenLetters) == 0:
        res1 = []
        for word in rem:
            for x in range(len(greenPos)):
                addword = True
                if not word[greenPos[x]] == greenLetters[x]:
                    addword = False
            if addword:
                res1.append(word)
        return res1
    else:
        return rem


def clearStep(rm, lP):
    hold = clearGrey(rm, lP)
    hold = clearYellow(hold, lP)
    return clearGreen(hold, lP)


def updateGreen():
    for i, v in enumerate(letterPos):
        if v[1] == 3:
            greens[v[0]] = alphaList[i]


# initialize surface and start the main loop
surface = pygame.display.set_mode((resX + UI, resY))
running = True
# --------------------------------------- Main Loop ---------------------------------------
while running:
    mouse = pygame.mouse.get_pos()  # puts the mouse position into a 2d tuple

    # ------------------------------------- input handling -------------------------------------
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
            break
        # ------------------------------------ mouse click actions ------------------------------------
        if event.type == pygame.MOUSEBUTTONDOWN:
            if input_rect.collidepoint(event.pos):
                active = True
                tColor = (100, 100, 200)
            else:
                active = False
                tColor = (255, 255, 255)
        # ------------------------------------ key press actions ------------------------------------
        if event.type == pygame.KEYDOWN:

            if active:  # if textbox is active
                if event.key == pygame.K_BACKSPACE:
                    user_text = user_text[:-1]
                    break

                if event.key == pygame.K_RETURN:
                    if len(user_text) < 5:
                        print(user_text, ' is not five letters.')
                    elif user_text.lower() not in dictionary:
                        print(user_text, ' is not a recognized word.')
                    else:
                        guesses.append(user_text.lower())
                        result = checkAnswer(guesses)
                        updateStep = True
                    break

                if len(user_text) < 5:
                    user_text += event.unicode
            if not active:
                if event.key == pygame.K_d:  # suggest next word
                    nextGuess = True
                if event.key == pygame.K_r:  # random word
                    randomRequest = True
                if event.key == pygame.K_t:  # enter suggested word
                    guesses.append(recc)
                    result = checkAnswer(guesses)
                    updateStep = True
                if event.key == pygame.K_g:  # update greens
                    updateGreen()

    # ---------------------------------------- Update Parameters ----------------------------------------
    for x in range(5):  # updates colors
        for y in range(6):
            colors[x][y] = clrs[result[x][y]]

    for y, word in enumerate(guesses):
        for x, letter in enumerate(word):
            letterStatus[alphaList.index(letter)] = result[x][y]
            letterPos[alphaList.index(letter)] = [x, result[x][y]]

    if nextGuess:
        updateGreen()
        if '' not in greens:
            recc = ''
            for l in greens:
                recc += l
            holdold = 420
        else:
            remainingWords = clearStep(remainingWords, letterPos)
            if len(remainingWords) > 1:
                holdold = len(remainingWords)
                remainHold = []
                for x in remainingWords:
                    remainHold.append(x)
                count = 0
                for word in remainingWords:
                    compWord = word
                    for index, word in enumerate(remainingWords):
                        for x in range(26):
                            letterHold.append([0, 0])
                        guessHold.clear()
                        for x in guesses:
                            guessHold.append(x)
                        guessHold.append(word)
                        resultHold = checkAnswer(guessHold)
                        for y, word in enumerate(guessHold):
                            for x, letter in enumerate(word):
                                letterHold[alphaList.index(letter)] = [x, resultHold[x][y]]
                        hold = clearStep(remainHold, letterHold)
                        letterHold.clear()
                        if len(hold) < holdold:
                            holdold = len(hold)
                            recc = word
            else:
                recc = remainingWords[0]
                holdold = 1337

        compWord = compHold
        nextGuess = False

    if updateStep:
        remainingWords = clearStep(remainingWords, letterPos)
        updateStep = False
        sampleWord = str(remainingWords[random.randrange(len(remainingWords))])
    if randomRequest:
        sampleWord = str(remainingWords[random.randrange(len(remainingWords))])
        randomRequest = False
    # ---------------------------------------- Rendering ----------------------------------------
    surface.fill((0, 0, 0))  # resets the screen to black

    # this is the white outlines
    for x in range(5):
        for y in range(6):
            pygame.draw.rect(surface, (255, 255, 255), (squareWidth * x, squareWidth * y, squareWidth, squareWidth),
                             width=borderWidth)
    # this is the color of the squares
    for x in range(5):
        for y in range(6):
            pygame.draw.rect(surface, colors[x][y], (
                borderWidth + squareWidth * x, squareWidth * y, squareWidth - borderWidth, squareWidth - borderWidth))

    base_font = pygame.font.Font(None, 150)
    # text in the squares
    for x in range(5):
        for y in range(len(guesses)):
            text = base_font.render(guesses[y].capitalize()[x], True, (255, 255, 255))
            surface.blit(text, (borderWidth + squareWidth * x, squareWidth * y))
    # the input box
    pygame.draw.rect(surface, tColor, input_rect)
    # text in the input box
    text = base_font.render(user_text, True, (0, 0, 0))
    surface.blit(text, input_rect)
    # letters status:
    for i, letter in enumerate(alphaList):
        base_font = pygame.font.Font(None, 20)
        text = base_font.render(letter, True, clrsStat[letterStatus[i]])
        surface.blit(text, (10 + resX + 10 * i, squareWidth + 20))
    # number of remaining:
    base_font = pygame.font.Font(None, 20)
    leng = len(remainingWords)
    lin = f"{leng}, it would take about {(leng ** 1.3) / 200} seconds to guess a new word"
    text = base_font.render(lin, True, (255, 255, 255))
    surface.blit(text, (10 + resX, squareWidth + 40))
    # reccomended guess:
    text = base_font.render(recc, True, (0, 0, 255))
    surface.blit(text, (10 + resX, squareWidth + 70))
    # random remaining word:
    if len(remainingWords) > 10:
        text = base_font.render(sampleWord, True, (255, 255, 255))
        surface.blit(text, (10 + resX, squareWidth + 90))
    else:
        for i, v in enumerate(remainingWords):
            text = base_font.render(v, True, (255, 255, 255))
            surface.blit(text, (10 + resX, squareWidth + 90 + 20 * i))
    pygame.display.flip()
