# 파이썬으로 내가 만들어보고 싶은 것 만들기

```
import random
import csv


stages = [
    """
    ------
      ㅡ  |
          |
          |
          |
          |
    ------
    """,
    """
    ------
    | ㅡ  |
          |
          |
          |
          |
    ------
    """,
    """
    ------
    | ㅡ  |
    O     |
          |
          |
          |
    ------
    """,
    """
    ------
    | ㅡ  |
    O     |
    |     |
          |
          |
    ------
    """,
    """
    ------
    | ㅡ  |
    O     |
   /|     |
          |
          |
    ------
    """,
    """
    ------
    | ㅡ  |
    O     |
   /|\\   |
          |
          |
    ------
    """,
    """
    ------
    | ㅡ  |
    O     |
   /|\\   |
   /      |
          |
    ------
    """,
    """
    ------
    | ㅡ  |
    O     |
   /|\\   |
   / \\   |
          |
    ------
    """,
    """
    ------
    | ㅡ  |
    O     |
   /|\\   |
   / \\   |
   _      |
    ------
    """,
    """
    ------
    | ㅡ  |
    O     |
   /|\\   |
   / \\   |
   _ _    |
    ------
    """
]

def word_data_list(csv_file):
    with open(csv_file, encoding="utf-8") as fr: # CSV 파일을 열고 단어 데이터 불러오기
        word_data = []
        reader = csv.reader(fr)
        next(reader)
        for row in reader:
            word_data.append(row) # CSV파일 각 행을 word_data에 추가
        return word_data

def choose_word(word_data): # word_data 리스트에서 랜덤으로 한 단어 선택
    return random.choice(word_data)

def select_difficulty():                                     # 게임 난이도 설정
    print("난이도를 선택하세요: 쉬움(e), 보통(m), 어려움(h): ")
    while True:
        difficulty = input("난이도 (e/m/h): ").lower()
        if difficulty in ['e', 'm', 'h']:
            return difficulty
        print("잘못된 입력입니다. 다시 입력해주세요.")

def get_attempts(difficulty):         # 선택한 난이도에 따라 주어지는 시도 횟수 결정
    if difficulty == 'e':
        return 15
    elif difficulty == 'm':
        return 10
    elif difficulty == 'h':
        return 5

def run_game(selected_word, hint, selected_category, attempts):
    display = ['_' if char != " " else " " for char in selected_word]   # 단어를 밑줄로 표시 (공백은 그대로 유지하기)
    guessed_letters = set()   # 사용자가 추측한 글자를 저장할 집합 
    incorrect_attempts = 0    # 틀린 시도 횟수 초기화
    hint_used = False

    while attempts > 0:
        print("\n현재 단어:", ' '.join(display))                        # 현재까지 맞춘 단어 상태
        print(f"남은 시도 횟수: {attempts}")                            # 남은 시도 횟수
        print(f"추측한 글자: {', '.join(sorted(guessed_letters))}")     # 사용자가 추측한 글자
        if hint_used:                                                  # hint를 입력한 경우 출력
            print(f"힌트: {hint}")                                      
        print(stages[incorrect_attempts])                              # 틀린 횟수에 따른 그림
        
        user_input = input("알파벳을 입력하세요: ").lower()
    
        if user_input == "hint":           # 사용자가 'hint'를 입력한 경우
            attempts -= 1
            hint_used = True
            print(f"힌트: {hint}") 
            continue
    
        if user_input == selected_word:    # 사용자가 단어 전체를 맞춘 경우
            print("\n 정답입니다!", selected_word)
            break
            
        if len(user_input) != 1 or not user_input.isalpha():     # 사용자의 입력이 한 글자가 아니거나 알파벳이 아닌 경우
            print("한 글자만 입력해주세요.")
            continue
    
        if user_input in guessed_letters:  # 이미 추측한 글자일 경우
            attempts -= 1
            print("이미 추측한 글자입니다.")
            continue
    
        guessed_letters.add(user_input)    # 추측한 글자를 집합에 추가
    
        if user_input in selected_word:                  # 추측한 글자가 단어에 포함된 경우
            for i, letter in enumerate(selected_word):
                if letter == user_input:                 # 맞춘 글자일 경우 밑줄을 맞춘 글자로 변경
                    display[i] = user_input
            if '_' not in display:                       # 정답을 모두 맞춘 경우
                print("\n정답입니다!", selected_word)
                break
        else:
            attempts -= 1                                # 추측한 글자가 단어에 없는 경우
            incorrect_attempts += 1
            print(f"틀렸습니다! 남은 시도 횟수: {attempts}")
            
    if attempts == 0:                                    # 시도 횟수를 다 사용한 경우
        print(f"정답은 '{selected_word}'입니다!")
        return False


def hangman_game():

    difficulty = select_difficulty()
    attempts = get_attempts(difficulty)

    word_data = word_data_list(csv_file)          # CSV파일에서 단어 데이터 읽어오기
    selected_word_list = choose_word(word_data)   # 단어 데이터에서 무작위로 단어 하나 선택
    selected_word = selected_word_list[0]         # 선택된 단어
    selected_category = selected_word_list[1]     # 단어 카테고리
    hint = selected_word_list[2]                  # 단어 힌트

    print(f"주제: {selected_category}")                           # 선택된 단어 카테고리 보여주기
    print(f"#힌트가 필요하면 'hint'를 입력해주세요.(기회1번 차감)")   # 힌트 사용법

    if run_game(selected_word, hint, selected_category, attempts):
        print("")
    else:
        print("")

csv_file = "./data/weekly_project12.csv"

```