---
authors: ["Vanitas",]
title: "Road to OSEP: แนะนำการเตรียมตัวเพื่อเรียนและสอบ OSEP"
date: 2022-03-06T14:51:36+07:00
description: "แนะนำคอร์สและเนื้อหาของ OSEP การเตรียมตัวและเทคนิคการสอบ"
---

มีพี่ๆ น้องๆ บางท่านหลังไมค์มาถามผมอยู่บ่อยครั้งว่าสำหรับคนที่เรียน OSCP ผ่านมาแล้วและมีความสนใจใน OSEP ว่า OSEP มันมีความเหมือนหรือแตกต่างกับ OSCP อย่างไร เข้าเรียนในคอร์สแล้วได้ประโยชน์อะไรและควรเตรียมตัวอย่างไรในการสอบเพื่อให้ได้มาซึ่ง cerificate ผมเลยขอถือโอกาสนี้มาเขียนรีวิวเป็นภาษาไทยให้ได้อ่านกันนะครับ 😀

หวังว่าผู้ที่สนใจหรือกำลังหาแรงบันดาลใจ อยากเติมไฟให้ชีวิต หรือยังไม่มีเป้าหมายว่าควรจะพัฒนา skill ตรงไหนต่อ จะได้รับการเติมไฟและ passion หลังจากที่ได้อ่านเช่นเดียวกับที่ผมได้รับครับ

บทความนี้ยาวจริงๆ นะ เราเตือนคุณแล้ว 😉

## 0x01 - What is OSEP?

![](../../../images/uploads/a19ddbe22afc139c538bfca41cea40ad.png)

OSEP เป็น course ตระกูล 30x หรือ expert level ของค่าย Offensive Security ซึ่งการได้ certificate ตัวนี้มาจะต้องผ่านการเรียน course PEN-300: Evasion Techniques and Breaching Defenses และผ่านการเข้าสอบในลักษณะ hands-on examination ได้ตามเกณฑ์ที่ทาง Offensive Security เจ้าของค่ายกำหนด โดยทางเจ้าของค่ายเองไม่ได้กำหนดเป็น pre-requisite ว่าเราต้องผ่าน OSCP (PEN-200) ก่อนแต่อย่างใด แต่ในมุมของผมเองแล้ว **หลายๆ อย่างใน course นี้ “จำเป็นต้องใช้” หลายๆ methodology จาก OSCP เลยคิดว่าถ้าผ่านมาก่อนจะเป็นอะไรที่ดีมากครับ**

ตัว course นั้น มุ่งเน้นไปตั้งแต่การโจมตีจากภายนอก ทั้งในรูปแบบของ external pentesting, client-side attack จนเราสามารถเข้าไปอยู่ใน internal network ได้แล้ว และพยายามจะ bypass protection ใน network นั้นๆ ที่เสมือนว่าเป็น security control ขององค์กรจริงๆ เพื่อ lateral movement ไปจนสุดทาง และสามารถยึดครอง domain admin จาก domain เป้าหมายได้ ทำให้ course นี้ค่อนข้างมีประโยชน์อย่างมาก สำหรับคนที่ต้องการเพิ่ม knowledge ในการทำ external pentest, network pentest จนไปถึงการทำ red team operation เลยทีเดียว

ในแง่ของ package การ enroll ทั่วๆ ไป ก็จะมีแบบ 60 days และ 90 days ซึ่งในส่วนนี้ คงแนะนำลำบากว่าควรเลือก 60 หรือ 90 วันดี เนื่องจากภาระหน้าที่และเวลาว่างในแต่ละวันของแต่ละคนไม่เท่ากัน จากความเห็นส่วนตัวของผมเองนั้น หากผู้ที่สนใจ enroll course นี้ มีความรู้ด้าน red team operation หรืออย่างน้อยๆ เคยทำ internal pentest ไปจนถึงมีประสบการณ์การทดสอบเจาะระบบที่เกี่ยวข้องกับ Active Directory อยู่ก่อนแล้ว 60 วันก็ค่อนข้างเพียงพอครับ

## 0x02 - Preparation

### **0x02.1 – Pre-requisite Knowledges**

อีกหนึ่งคำถามยอดฮิตที่มักถูกถามกันมาคือ ควรจะมีความรู้ใดก่อนถึงจะเริ่มคอร์สนี้ได้บ้าง? อันนี้ตอบได้เลยว่า “ไม่มีก็เริ่มได้ครับ” ทาง Offensive Security ค่อนข้าง define structure ในแต่ละ lesson ไว้ดี และค่อนข้างละเอียด จึงไม่ใช่ปัญหากับมือใหม่แต่อย่างใด เรียกว่า beginner level แต่ทุ่ม effort หนักๆก็สามารถที่จะเข้าใจในเบื้องต้น และไป research ต่อยอดต่อได้ครับ เรื่องนี้ต้องชื่นชม Offensive Security เลยจริงๆ

### **0x02.2 – Preparation & Practice**

สำหรับ course ที่เป็นต่างค่าย แต่มีเนื้อหาที่ค่อนข้างใกล้เคียงหรือมี infrastructure lab ที่ค่อนข้างช่วยฝึกมือเราได้ก่อนที่จะไปเริ่ม ผม list มาให้ประมาณนี้ครับ เผื่อถ้าสนใจก็ลองไปลงกันได้ดูครับ

***หมายเหตุ :** Level definition เป็น opinion ส่วนตัวของผมเอง ไม่อยากให้ยึดถือว่าจะต้องเป็นตามนั้นนะครับ ทั้งนี้ทั้งนั้น ผม define มาให้เพื่อแบ่งระดับความยาก-ง่าย เผื่อผู้สนใจจะได้เลือกลงตามระดับที่ตัวเองอยากเรียนได้ครับ*

- **Course**
    - Certified Redteam Professional ([https://www.pentesteracademy.com/activedirectorylab](https://www.pentesteracademy.com/activedirectorylab)) – Similar to OSEP level
    - Certified Redteam Expert ([https://www.pentesteracademy.com/redteamlab](https://www.pentesteracademy.com/redteamlab)) – Beyond from OSEP
    - PACES ([https://www.pentesteracademy.com/gcb](https://www.pentesteracademy.com/gcb)) – Much more beyond from OSEP
    - Certified Redteam Operator – Red Team Ops ([https://courses.zeropointsecurity.co.uk/courses/red-team-ops](https://courses.zeropointsecurity.co.uk/courses/red-team-ops)) – Similar to OSEP level
- **Infrastructure Lab**
    - Offshore - HTB Pro Lab ([https://app.hackthebox.com/prolabs/offshore](https://app.hackthebox.com/prolabs/offshore)) – Similar to OSEP level
    - RastaLabs - HTB Pro Lab ([https://app.hackthebox.com/prolabs/rastalabs](https://app.hackthebox.com/prolabs/rastalabs)) – Similar to OSEP level
    - Cybernetics – HTB Pro Lab ([https://app.hackthebox.com/prolabs/cybernetics](https://app.hackthebox.com/prolabs/cybernetics)) – Beyond from OSEP
    - APTLabs – HTB Pro Lab ([https://app.hackthebox.com/prolabs/aptlabs](https://app.hackthebox.com/prolabs/aptlabs)) – Much more beyond from OSEP

นอกเหนือจากคอร์สตามที่ให้รายการไว้ด้านบนแล้ว อีกทักษะหนึ่งที่เราสามารถ practice เองได้คงเป็นเรื่อง offensive tradecraft ที่ใน course เราต้อง take effort ในการเขียน C# code เพื่อเรียกใช้งาน Windows API ดังนั้นจึงขอแนะนำให้ฝึกทักษะที่เกี่ยวข้องกับ C#/.NET และความคุ้นในการนำมาใช้เพื่อพัฒนา shellcode ตามแหล่งดังนี้ครับ

- C# Workshop – Defcon ([https://github.com/un4ckn0wl3z/defcon27_csharp_workshop](https://github.com/un4ckn0wl3z/defcon27_csharp_workshop))
- AfricaCon 2021 – MalwareDev ([https://github.com/chr0n1k/AH2021Workshop](https://github.com/chr0n1k/AH2021Workshop))

## 0x03 - Course Syllabus & Content

สำหรับ course ของ Offensive Security ในแต่ละบท จะแบ่งเป็นสามส่วนหลักๆ คือ ส่วนของเนื้อหา (course content), ส่วนของ exercise เพื่อให้ผู้เรียนได้ฝึกทำตามหรือลอง modify ตาม objective ในแต่ละส่วน และส่วนสุดท้ายคือ extra miles ที่จะใช้ฝึกให้ผู้เรียน คิด และหัด ในระดับที่เหนือขึ้นไปกว่า exercise ในบทเรียนนั้นๆ

โดยสิ่งที่จะมาพร้อมกับ course คือ PDF จำนวน 700+ หน้า และ video อีกประมาณ 20 hrs เรียกว่าหนักๆ แน่นๆ กันไปเลย

ในส่วนของ outline ของแต่ละบท ผมพยายามเขียนเนื้อหาและประโยชน์ที่เราจะได้เรียนรู้ ในแต่ละบท (แบบไม่ spoil) ดังนี้ครับ

1. **Client Side Code Execution : MS Office** – บทนี้พูดถึงในเรื่องการ craft malicious payload ด้วยไฟล์เอกสารที่เป็น format ของ Microsoft Office 
โดยเรามักจะพบเจอได้บ่อยในเรื่องของ phishing malware (หรือเรียกสั้นๆ แพร่หลายว่า MalDoc) ที่ threat actor นิยมใช้ในการโจมตีเหยื่อโดยการส่งไฟล์เอกสารแปลกปลอมแนบมาทาง e-mail เพื่อหลอกให้เหยื่อ download มาเปิด และถูกควบคุมเครื่องในท้ายที่สุด
2. **Client Side Code Execute: Windows Script Host** – บทนี้มีคล้ายคลึงกับบทแรก เพียงแต่เป็นการอีกวิธีการในการหลอก เพื่อ compromise เครื่องเหยื่อ โดยเปลี่ยนจาก MalDoc มาเป็น Windows Script Host ที่มักพบ threat actor ใช้กันบ่อย เช่น ไฟล์นามสกุล .js, .hta, .lnk เป็นต้น เมื่อเหยื่อถูกหลอกให้เรียกไฟล์เหล่านี้ ก็จะถูกควบคุมเครื่องไปได้ โดยในเนื้อหาของ บทที่ 1 และบทที่ 2 ก็จะมีการสอดแทรกให้ผู้เรียนได้ลอง craft payload พร้อมกับทำความเข้าใจว่ามันสามารถไปเรียก Windows API เพื่อก่อให้เกิดผลลัพธ์ที่เราต้องการได้อย่างไรด้วยเช่นกัน
3. **Process Injection and Migration** – บทนี้จะเป็นการบอกเล่าเทคนิคในการหลบหลีกการตรวจจับด้วย anti-virus เบื้องต้น ให้สามารถรัน shellcode ที่เราเตรียมไว้ บน process ที่เราต้องการได้ ซึ่งเทคนิค process injection นี้ถูกใช้กันแพร่หลายอย่างมากในกรณีที่ threat actor สามารถ compromise เครื่องเหยื่อได้แล้ว และต้องการจะฝังตัวระยะยาวเพื่อหลบหลีกการตรวจจับ แล้วไปดำเนินการต่อ
4. **Introduction to Antivirus Evasion** – บทนี้จะสอนให้เราได้เรียนรู้วิธีการ modify payload หรือ code สำหรับหลบหลีกการตรวจจับด้วย anti-virus เบื้องต้น ซึ่งในบทจะสอนทั้งในรูปแบบของ PowerShell, .NET และ VBA เลยทีเดียว รวมๆแล้วเนื้อหาในบทนี้ มีประโยชน์สำหรับการใช้งานในชีวิตจริงมากๆเลยครับ
5. **Advanced Antivirus Evasion** - บทนี้กล่าวถึงบทความ research ในความพยายาม debug anti-virus ที่เราสนใจว่าหากต้องการจะ bypass เราจะมี methodology ในการ bypass มันอย่างไรได้บ้าง โดยตัวอย่างที่ถูกหยิบยกมาเป็น AMSI (Anti-malware Scan Interface) ซึ่งเป็น malware detection technology ของ Windows เอง ดังนั้นสำหรับบทนี้ คนที่คุ้นเคยกับการทำ AMSI bypass ก็จะเข้าใจและนำไปใช้ได้อย่างไม่ยากนัก
6. **Application Whitelisting** – บทนี้กล่าวถึง methodology เบื้องต้น ที่ใช้ในการ bypass AppLocker ซึ่งเป็น application whitelist policy ของ Windows OS ซึ่งค่อนข้างมีหลากหลายวิธีการ และมีประโยชน์มากทีเดียวครับ
7. **Bypass Network Filters** – บทนี้กล่าวถึงในเรื่องของการ bypass network proxy รวมไปถึงการดักจับ activity ต่างๆของเรา ผ่าน network layer สำหรับบทนี้ไม่ได้มีอะไรซับซ้อนมากนัก แต่ก็ค่อนข้างมีประโยชน์กับการใช้งานในชีวิตจริง
8. **Linux Post-Exploitation** – บทนี้จะสอนวิธีการในการค้นหาข้อมูลจากเครื่อง Linux server ในกรณีที่เราสามารถ compromise ได้แล้วว่าจะค้นข้อมูลบนเครื่องเพื่อนำไปใช้ประโยชน์ต่อได้อย่างไร รวมถึงสอนวิธีการ craft malware ที่สามารถหลบหลีกการตรวจจับ anti-virus บน Linux system เบื้องต้นได้อีกด้วย
9. **Kiosk Breakouts** – บทนี้จะสอน methodology ในการ breakout / escape เครื่อง Linux Kiosk เช่น ถ้าถูก hardening เพียงเพื่อเปิดได้เพียง browser อย่างเดียว จะทำอย่างไรถึงจะใช้จุดอ่อนตรงนี้นำไปสู่การยึดเครื่อง Kiosk ทั้งเครื่องได้บ้าง
10. **Windows Credentials** – บทนี้จะสอนในเรื่องของ Token access บน Windows OS รวมถึงวิธีการที่จะขโมย credential จากเครื่อง และไปสวมรอยเป็น account ที่เราต้องการบน Windows อย่างไรได้บ้าง
11. **Windows Lateral Movement** - บทนี้ กล่าวถึงวิธีการในการ pivot network เพื่อให้สามารถเข้าถึง network zone ต่างๆภายในองค์กรของเหยื่อได้ โดยที่เสมือนเรายังนั่งอยู่ที่ public internet ซึ่ง knowledge ตรงนี้มีประโยชน์อย่างมากกับการทำงานในชีวิตจริงสำหรับผู้ที่ทำ external pentest หรือการทำ red team Operation เองก็ตาม
12. **Linux Lateral Movement** – บทนี้กล่าวถึงการโจมตีเพื่อยึดกรณีที่องค์กรนี้มีการใช้งาน DevOps tools รวมถึงการใช้งาน Kerberos บน Linux
13. **Microsoft SQL Attack** – บทนี้จะสอนการค้นหาข้อมูล และโจมตีสำหรับเป้าหมายที่ใช้งาน database server เป็น Microsoft SQL รวมถึงเทคนิคต่างๆที่สามารถนำมาใช้โจมตีร่วมกันเป็น sequence เดียว ผ่าน .NET code ได้ รวมถึงสอนวิธีในการ lateral movement ผ่านทาง SQL Server ไปยัง SQL server เครื่องอื่นด้วยเช่นกัน
14. **Active Directory Exploitation** – บทนี้จะสอนวิธีการในการค้นหาข้อมูล จาก domain และ Active Directory เพื่อหาไป compromise domain controller บน domain เป้าหมาย เพื่อยึด domain admin ซึ่งถือเป็น ultimate goal ในการยึดเครือข่ายและระบบของทั้งองค์กรได้
15. **Combining The Pieces** – ในส่วนนี้จะเป็นการรวบรวม knowledge จากหลายๆ บทที่ได้เรียนมา แล้วจำลองเป็น lab เล็กๆ เพื่อทดสอบโจมตีตังแต่อยู่ใน public network จนเข้าไปยึด domain admin จากภายในได้ ซึ่งบทนี้จะมีเป้าหมายให้เราได้ combine knowledge ทั้งหมด ว่าสามารถทำอะไรในแต่ละสถานการ์ได้บ้าง เพียงแต่จะไม่ได้ complex ถึงระดับที่เราจะเจอใน lab หรือ challenge ซึ่งจะกล่าวใน section ถัดไป

## 0x04 - Lab Time and Challenges

ในส่วนของ lab สำหรับ course นี้ จะเรียกว่า “Challenge” (ส่วน lab ปกติจะเรียกว่า exercise แทน งงใช่มะ -_-? ผมก็งงแหละ) ซึ่งจะมีทั้งหมด 6 challenge ด้วยกัน ในแต่ละ challenge จะถูกสมมุติให้เราอยู่ใน scenario ในแต่ละสถานการณ์ที่แตกต่างกัน จะแบ่งเป็น 3 challenge แรก เป็น scenario ที่ค่อนข้างสั้น และ 3 challenge หลัง จะค่อนข้างยาว ซึ่งมีเพียง challenge ที่ 6 เท่านั้น ที่ค่อนข้างใหญ่

สำหรับคำแนะนำในเรื่องของ challenge นี้ โดยความเห็นส่วนตัวคือไม่ว่าจะ challenge เล็กหรือใหญ่ก็มักจะต้องใช้ knowledge จากหลายๆบท มารวมๆกัน เพราะงั้นจึงอยากให้เรียน course และทำ exercise ทั้งหมดมาก่อน แล้วค่อยอดใจเพื่อมาเริ่มทำ challenge ทั้งหมด จะสนุกและครอบคลุมที่สุดครับ

ในแต่ละเครื่อง ก็จะมี flag อยู่ 2 ประเภทให้เรา submit ลงใน portal โดย local.txt จะได้มาในกรณีที่เราสามารถ compromise ในระดับสิทธิ์ต่ำ (low privilege account) ในแต่ละเครื่องได้แล้ว และ proof.txt จะได้มาในกรณีที่เราได้สิทธิ์สูงสุด (root หรือ administrative privilege) ในแต่ละเครื่อง ซึ่งต้องบอกว่าในแต่ละ challenge ก็มีจำนวน flag มากน้อยไม่เท่ากัน ขึ้นอยู่กับจำนวนเครื่องใน challenge นั้นๆ ด้วย

![](../../../images/uploads/a736b38b8113444add273494b38e6b01.png)


ในส่วนของ Challenge รวมๆแล้วถือว่าใช้เวลาไม่เยอะ ถ้าเทียบกับ course และ exercise ที่ต้องทำทั้ง 15 บทที่กล่าวมาจาก section ที่แล้ว โดยรวมในความเห็นของผมเองจึงคิดว่าแอบน่าเสียดายเล็กๆ ที่มันน้อยไปหน่อยครับ ถ้ามีสัก 10 challenge หรือ เพิ่มความ complex ใน 5 challenge แรก น่าจะทำให้ผู้เรียนสนุกและได้ idea เพิ่มขึ้นมากกว่านี้

## **0x05 – Exam time & Game-On**

ด้วยความที่ทุกคนที่เข้ามาอ่าน น่าจะทราบกันอยู่แล้วว่า Offensive Security เป็นค่ายที่เน้นการทดสอบผู้เรียนแบบ hands-on เป็นหลัก ก็คือลงมือ hack กันโต้งๆตาม objective ที่ได้รับมาแบบไม่ต้องคิดเยอะ ดังนั้นแน่นอนว่าสิ่งที่สำคัญสำหรับการสอบ คือ **“ความอดทน ความมุ่งมั่น สมาธิ และความไม่ยอมแพ้”** (โคตรโชเน็นเลย -_-“ แต่เรื่องจริงนะครับ)

![](../../../images/uploads/b519e60a3d01a02740e3df8e10ec51f9.png)

*Gotta Pwn Them All !?*

โดยในรูปแบบของการสอบ จะเป็น proctored exam ความหมายคือ เราจะต้องเข้าไปทำการยืนยันตัวตนเราก่อนที่จะเริ่มสอบ โดยที่จะต้องเปิดกล้องอยู่ตลอดเวลาเพื่อให้ staff ของ Offensive Security ได้เห็นว่าเราทำอะไรบ้างในทุกช่วงเวลาการสอบ แน่นอนว่าเราสามารถที่จะบอกเค้าไป take break, กินข้าว, เดินเล่น, ล้างจานให้ภรรยา ฯลฯ whatever ได้ตามต้องการ แต่เราจะไม่สามารถเอามือถือของเรามาไว้ในห้องที่ทำการสอบได้เลย (Proctored Exam Guide : [https://help.offensive-security.com/hc/en-us/articles/360050293792-OSEP-Exam-Guide](https://help.offensive-security.com/hc/en-us/articles/360050293792-OSEP-Exam-Guide))

การสอบจะใช้เวลา 48 hrs (จริงๆแล้ว 47 hrs 45 minutes แต่ตีๆไปละ 48 ชม.ละกัน) เช่น ถ้าเราเริ่มสอบ เที่ยงวัน ของวันนี้ VPN ที่เราใช้เข้าไปสอบใน lab ของเค้าก็จะตัด ณ เวลา 11:45 AM ของอีกสองวันข้างหน้า ซึ่งความต่างของ 24 hrs testing กับ 48 hrs testing คือเรื่องของ mental และ motivation เป็นหลัก ดังนั้นสำหรับผมเองแล้ว ความล้าของร่างกาย และความกดดันทางสภาพจิตใจ ค่อนข้างมีผลมากเลยครับ ถ้า balance ได้ไม่ดี เราจะเกิดอาการรนได้อย่างไม่รู้ตัว (ในจุดนี้ คนที่เคยสอบ OSCP น่าจะเข้าใจอารมณ์ได้ดี) **สิ่งสำคัญคือ “Don’t think that breaking is waste your time, it is precious time to refresh your mental and mind” อย่าไปคิดว่าการเบรคบ่อยๆ หรือนานๆ มันเสียเวลาหรืออาจทำไม่ทันครับ บางทีเบรคบ่อยๆ นั่งกระดกกาแฟไปด้วย ทำไปด้วย มันจะผ่อน pressure เราออกไปเอง**

พูดถึงกติการสอบกันบ้างดีกว่า Objective ในการสอบจะมีหลักๆ 2 ข้อด้วยกันคือ

1. Archive 100 points in exam
2. Archive to gain secret flag

สำหรับ exam guide ดูได้ที่นี่ : [https://help.offensive-security.com/hc/en-us/articles/360050293792-OSEP-Exam-Guide](https://help.offensive-security.com/hc/en-us/articles/360050293792-OSEP-Exam-Guide)

Scenario ในการสอบ เราจะได้ ip ของ network ที่เราถูกตั้ง objective ขึ้นมา โดยต้องทำอย่างไรก็ได้ ให้สามารถบุกเข้าไปยึดภายในองค์กรที่เค้า assign มาให้เราได้ ไม่ได้เป็น standalone machine แบบที่เจอใน OSCP ในจุดนี้การทำ post-exploitation จะค่อนข้างมีผลอย่างมาก เช่นเดียวกันกับที่เราเจอใน challenge โดยสิ่งที่เราจะได้ในแต่ละเครื่อง (หรือมีบางเครื่องก็ตามที่ไม่มี flag เลย) จะมี flag ให้เราเข้าไปเก็บ และในทุกๆ flag จะได้รับ 10 points ในการ submit เข้า exam portal ซึ่งในจุดนี้ ไม่ว่าจะเป็น local หรือ proof flag ก็ตาม point จะไม่ต่างกัน ดังนั้นถ้า collect ได้ครบ 10 flag ก็เพียงพอที่จะผ่านครับ

อีกอย่างที่จะผ่านได้ โดยที่ได้ point ไม่ถึง 100 points คือเราจะต้องได้ flag: secret.txt เพื่อนำไป submit ลงบน exam portal โดยที่ objective ที่ให้มา จะไม่ได้บอกเราตรงๆว่า secret มันอยู่เครื่องไหน แต่เราจะรู้ได้เองด้วยชื่อ machine name ของมัน

ซึ่งไม่ควรต้องกังวลกับเรื่องเวลาว่าจะไม่พอ หาก prepare มาดีๆ ตัว exam content ก็ค่อนข้าง “doable” และสามารถมี point พอที่จะผ่านได้ไม่ยากใน เวลา 24 hrs แรกครับ

หลังจาก VPN ตัดไปแล้ว นอกเหนือจากที่เราจะต้องได้ point หรือ secret ตาม objective เรายังจะต้องทำ report เพื่อ submit ผ่าน report site ของ Offensive Security ไปให้เค้าเพื่อทำการ grading ภายใน 24 hrs หลังจาก VPN ได้ถูกตัดไปแล้วด้วยเช่นกัน

![](../../../images/uploads/155fa896e4ef01799574addb887af07e.png)

*ความรู้สึกตอนทำ report T_T*

หากเรา ensure เรียบร้อยว่า report เราครบ หลังจากส่งไปเราจะต้องรอเมล์ยืนยัน เพื่อ confirm ว่าเราผ่านหรือไม่ โดย Offensive Security เค้าจะ officially ว่าจะตอบผลเรามาภายใน 10 business days แต่เอาจริงๆก็ไม่เกิน 3-5 วันครับ โดยประมาณ

ซึ่งถ้าผ่าน ก็จะได้ badge ประมาณนี้ ท๊าดา~~~

![](../../../images/uploads/f4316ef91a7953f0128f38b55f61a092.png)

## 0x06 - Conclusion

วมๆแล้ว ก็น่าประทับใจตามมาตรฐานของค่าย Offensive Security ครับ ถ้าลองมานั่งนึกย้อน ข้อดี ข้อเสีย ดีๆ น่าจะ list ได้ประมาณนี้

**Pros:**
1. Course content ค่อนข้างแน่น และละเอียด
2. Video content ค่อนข้างละเอียด ดูแล้วทำตามได้ง่าย
3. Friendly to beginner แม้ไม่เคยแตะมาก่อน
4. สามารถ bypass AV เบื้องต้นได้แน่นอน ยิ่ง AMSI นี่น่าจะสบายๆ
5. ตัว course จะสอน methodology ที่เราสามารถไปต่อยอด และ develop offensive tradecraft ได้เอง
6. เป็น course introduce ที่ดี ที่จะต่อยอดไปใน path ของ red team operation
7. ตัว certificate ค่อนข้างถูก recognize ในฝั่ง EU region

**Cons:**
1. ราคา course ค่อนข้างสูง (90 days with 1500 USD โดยประมาณ)
2. Lab challenge ค่อนข้างสั้น และน้อย ถ้าเทียบกับ content ของเนื้อหา
3. ตัว certificate อาจจะยังไม่ได้เป็น well-known ในเมืองไทยเอง คนรู้จักยังน้อย

## 0x07 - Essential Resources & References

Section นี้จะพยายามรวบรวม repo ที่ค่อนข้างมีประโยชน์มาให้ครับ หวังว่าจะมีประโยชน์สำหรับคนที่สนใจครับ

- [https://github.com/Octoberfest7/Tools](https://github.com/Octoberfest7/Tools)
- [https://github.com/chvancooten/OSEP-Code-Snippets](https://github.com/chvancooten/OSEP-Code-Snippets)
- [https://github.com/un4ckn0wl3z/defcon27_csharp_workshop](https://github.com/un4ckn0wl3z/defcon27_csharp_workshop)
- [https://github.com/chr0n1k/AH2021Workshop](https://github.com/chr0n1k/AH2021Workshop)
- [https://github.com/S3cur3Th1sSh1t/OffensiveVBA](https://github.com/S3cur3Th1sSh1t/OffensiveVBA)
- [https://github.com/outflanknl/EvilClippy](https://github.com/S3cur3Th1sSh1t/OffensiveVBA)
- [https://github.com/med0x2e/GadgetToJScript](https://github.com/med0x2e/GadgetToJScript)
- [https://github.com/3xpl01tc0d3r/ProcessInjections](https://github.com/3xpl01tc0d3r/ProcessInjections)
- [https://github.com/optiv/ScareCrow](https://github.com/optiv/ScareCrow)

# 0x08 - Contact Me

สำหรับคนที่สนใจและต้องการข้อมูลเพิ่มเติม สามารถขอ contact ผมผ่านแอดมิน [Cybersec Career Kickstart](https://www.facebook.com/cyberseccareerkickstart/) หรือ [suam.wtf](https://suam.wtf) ได้เลยครับ 😉 ยินดีแลกเปลี่ยนความรู้กันเสมอนะครับ
