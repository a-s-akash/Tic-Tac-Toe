import cv2
import mediapipe as mp
import math
import time

mp_hands = mp.solutions.hands
hands = mp_hands.Hands()
cap = cv2.VideoCapture(0)

xx = ['-','-','-','-','-','-','-','-','-']
typed = []
def get(ox):
    global typed
    global xx
    values = []
    is_read = 0
    while cap.isOpened():
        ret, frame = cap.read()
        if not ret:
            values.append("Failed to capture frame")
            break
        frame = cv2.flip(frame, 1)
        rgb_frame = cv2.cvtColor(frame, cv2.COLOR_BGR2RGB)
        results = hands.process(rgb_frame)
        if results.multi_hand_landmarks:
            is_read = 1
            for hand_landmarks in results.multi_hand_landmarks:
                index_finger_tip = hand_landmarks.landmark[mp_hands.HandLandmark.INDEX_FINGER_TIP]
                index_finger_base = hand_landmarks.landmark[mp_hands.HandLandmark.INDEX_FINGER_MCP]
                thumb_tip = hand_landmarks.landmark[mp_hands.HandLandmark.THUMB_TIP]
                tip_x, tip_y = int(index_finger_tip.x * frame.shape[1]), int(index_finger_tip.y * frame.shape[0])
                base_x, base_y = int(index_finger_base.x * frame.shape[1]), int(index_finger_base.y * frame.shape[0])
                thumb_x, thumb_y = int(thumb_tip.x * frame.shape[1]), int(thumb_tip.y * frame.shape[0])
                if abs(tip_x - thumb_x) < 20 and abs(tip_y - thumb_y) < 20:
                    values.append("5")
                else:
                    cv2.line(frame, (base_x, base_y), (tip_x, tip_y), (0, 255, 0), 2)
                    if tip_x < base_x - 20:
                        direction = "left"
                    elif tip_x > base_x + 20:
                        direction = "right"
                    else:
                        direction = "middle"
                    if tip_y < base_y - 50 and direction == "middle":
                        values.append("2")
                    elif tip_y > base_y + 50 and direction == "middle":
                        values.append("8")
                    else:
                        if hand_landmarks.landmark[mp_hands.HandLandmark.WRIST].x < 0.5:
                            if tip_y < base_y - 20:
                                values.append("3")
                            elif tip_y > base_y + 20:
                                values.append("9")
                            else:
                                values.append("6")
                        else:
                            if tip_y < base_y - 20:
                                values.append("1")
                            elif tip_y > base_y + 20:
                                values.append("7")
                            else:
                                values.append("4")
        else:
            if is_read == 1:
                s = list(set(values))
                max = 0
                max_val = 0
                for i in s:
                    if values.count(i) > max:
                        max = values.count(i)
                        max_val = i
                max_val = int(max_val)
                if max_val in typed:
                    print("Already Choosen")
                    return get(ox)
                if max_val not in typed:    
                    typed.append(max_val) 
                    xx[max_val-1] = ox
                    return
        cv2.imshow("Hand Detection", frame)
        if cv2.waitKey(1) & 0xFF == ord('q'):
            break
    cap.release()
    cv2.destroyAllWindows()
def check():
    global xx
    if xx[0]==xx[1]==xx[2] and xx[0]!='-':
        return xx[0]
    elif xx[3]==xx[4]==xx[5] and xx[3]!='-':
        return xx[3]
    elif xx[6]==xx[7]==xx[8] and xx[8]!='-':
        return xx[8]
    elif xx[0]==xx[3]==xx[6] and xx[3]!='-':
        return xx[3]
    elif xx[1]==xx[4]==xx[7] and xx[7]!='-':
        return xx[7]
    elif xx[2]==xx[5]==xx[8] and xx[2]!='-':
        return xx[5]
    elif xx[2]==xx[4]==xx[6] and xx[2]!='-':
        return xx[4]
    elif xx[0]==xx[4]==xx[8] and xx[8]!='-':
        return xx[4]
    elif '-' not in xx:
        return 'd'
    else:
        return '+'
def show():
    global xx
    values.append()
    for i in range(9):
        values.append('',xx[i],end=" ")
        if i!=2 and i!=5 and i!=8:
            values.append("|",end="")
        if i==2 or i==5:
            values.append("\n---+---+---")
    values.append()
def trophy(what):
    if what!='+':
        show()
        if what == 'O':
            values.append(f'O won the match...')
        elif what == 'X':
            values.append(f'X won the match.')
        elif what == 'd':
            values.append(f'Match ended with draw status.')
        exit(0)
i=0
while True:
    show()
    if i%2==0:
        values.append("Player O:")
        get('O') 
    else:
        values.append("Player X:")
        get('X')
    i+=1
    trophy(check())
