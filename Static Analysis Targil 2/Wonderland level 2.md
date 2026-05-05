# Reverse_Engineering_Task2

Wonderland level 2 - WriteUp

Aviya Yosef ID: 325481828

לאחר הכנסת הקלט 2, הוצגה ההודעה הבאה:

<img width="847" height="57" alt="image" src="https://github.com/user-attachments/assets/b5c0d551-0e93-4c69-a8c5-850f2b447f18" />

לאחר מכן בקוד יש את הבלוק הבא:

<img width="212" height="151" alt="image" src="https://github.com/user-attachments/assets/2a02d6e3-697e-4184-b646-15db89dbbcef" />

שהוא כנראה הוספה מיותרת של הקומפיילר ולא עושה משהו משמעותי. נעבור לבלוק הבא:

<img width="434" height="508" alt="צילום מסך 2026-05-05 125808" src="https://github.com/user-attachments/assets/9733a06b-a3fb-490f-96b6-3bb9d3390c14" />


ראיתי שיש קריאה לפונקציה fgets כלומר יש קריאה של קלט מהמשתמש, לפני כן נדחפה כתובת הבאפר

במיקום הארגומנט "buffer" של הפונקציה, לכן קלט המשתמש נכנס לתוך Buffer. 

לאחר מכן, ישנה קריאה לפונקציה strlen שמחזירה את אורך המחרוזת וכן ידוע שערך ההחזרה מוחזר בתוך 

הרגיסטר eax וכיוון שראיתי טעינה של eax לתוך var_C קראתי לו בשם "user_input_len".

בנוסף, אנו רואים אתחול של משתנה var_4 בערך 0, קיבלתי תחושה שמדובר בi - (אוסיף שבתצוגת הגרף 

יש מבנה של לולאה) וחיפשתי מקום בו הוא מקודם וכן מצאתי קידום ב-4 בבלוק הבא:

<img width="241" height="155" alt="image" src="https://github.com/user-attachments/assets/584ce642-c43b-4b0e-a044-4429c3c58550" /> 

כך נראה הבלוק לאחר הניתוח:

<img width="367" height="493" alt="image" src="https://github.com/user-attachments/assets/0d84e8e7-cb22-4171-b5b3-edf6ac87c2ba" />

מצאתי גם את הבדיקה של i מיד לאחר הבלוק הנ"ל וגם בלוק קידום הi חוזר לבלוק הבדיקה מה שמבטיח את

נכונות ההנחה שמדובר במבנה בסיסי של לולאה:

<img width="326" height="188" alt="image" src="https://github.com/user-attachments/assets/1f5649cb-c1a4-4b65-a043-7a14b692aacf" />

התנאי אומר: כל עוד אפשר להתקדם 4 בתים בקלט - תתקדם.

נתבונן בגוף הלולאה וכך נגיע לפענוח:

<img width="372" height="167" alt="image" src="https://github.com/user-attachments/assets/23310525-33d9-47a4-accd-f3224869bdce" />

נשים לב שכל פעם נלקח מהבאפר DWORD שגודלו 4 בתים ומתבצע איתו כל פעם XOR עם המספר 
0x41524241. 

לבסוף, אנו רואים בלוק שעושה השוואה למחרוזת "into the rabbit hole" ואם זה שווה - הצלחנו.

כיוון שפעולת XOR היא הפיכה, זאת אומרת שצריך לעשות בדיוק את פעולת הלולאה על המחרוזת הנ"ל.

נציג סקריפט בפייתון שעושה את זה ונסביר בתוך הערות הקוד:

    s = input("Enter string: ")

    b = bytearray(s, "latin-1") #בעצם כל תו הוא בדיוק בית אחד

    i = 0   # אתחול בדיוק כמו בתוכנית

     #כל עוד אפשר לקרוא 4 בתים מהקלט
    while i + 3 < len(b):           

     #מכניס לערך 4 בתים בפורמט ליטל אנדיאן        
        value = int.from_bytes(b[i:i+4], "little")  

        #עושים XOR עם המספר הנתון מהתוכנית
        
        value ^= 0x41524241

        #כותבים בחזרה לקלט את התוצאה
        
        b[i:i+4] = value.to_bytes(4, "little")

        #קידום ב4 כמו בתוכנית
        
        i += 4

     # הדפסת התוצאה
     
    print(b.decode("latin-1"))
    
 נראה את ריצת הסקריפט:

<img width="423" height="71" alt="image" src="https://github.com/user-attachments/assets/79be0537-dd9e-4626-afd5-09a5efba45e5" />

עכשיו נזין את הסיסמה לקוד של wonderland:

<img width="942" height="323" alt="image" src="https://github.com/user-attachments/assets/fe32e9d9-8447-42f8-bcab-dd83dbd91e61" />

הצלחנו בסייעתא דשמייא ועברנו בהצלחה לשלב 3!!



 


