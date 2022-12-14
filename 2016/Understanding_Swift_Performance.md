# Understanding Swift Performance

## ๐ Choosing the Right Abstraction Mechanism

์ค์ํํธ์ ์ฌ๋ฌ ํ์๋ค ์ค ์ด๋ค ํ์์ ๊ณจ๋ผ ์จ์ผ ํ ๊น?
`๋ชจ๋ธ๋ง`๊ณผ `์ฑ๋ฅ`์ ๊ณ ๋ คํ์ฌ ํ์์ ์ ํํ  ๊ฒ.

์ฑ๋ฅ์์ ์ด์ผ๊ธฐํ  3๊ฐ์ง : `Allocation` `Reference Counting` `Method Dispatch`

![](https://i.imgur.com/JjjTvwm.png)

์ด ์ธ ๊ฐ์ง ๊ด์ ์์ ํ์์ ์ฑ๋ฅ์ ํ๋จํ๊ฒ ๋๋ค.

- Allocation: ๋ฉ๋ชจ๋ฆฌ๊ฐ stack ๋๋ heap ์์ญ ์ค ์ด๋์ ํ ๋น ๋๋์ง
- Reference Counting: ์ฐธ์กฐ ๊ด๋ฆฌ
- Method Dispatch: ์ด๋ค ๋ฉ์๋๊ฐ ์คํ๋์ด์ผ ํ๋์ง๋ฅผ ์์๋ด๋ ๋ถ๋ถ

์ฐ์ธก์ผ๋ก ๊ฐ ์๋ก ์ฑ๋ฅ์ด ์ข์ง ์์์ ๋ํ๋ธ๋ค.

## ๐ Allocation

### Stack vs Heap

Stack

- Stack์ ์ปดํ์ผ ํ์์ ๊ทธ ํฌ๊ธฐ๊ฐ ์ ํด์ง๋ค
- Stack์ ๋ฉ๋ชจ๋ฆฌ ํ ๋น์ ์ํด stack ํฌ์ธํฐ๋ฅผ ๊ฐ์์ํค๊ณ , ๋ฉ๋ชจ๋ฆฌ ํด์ ๋ฅผ ์ํด stack ํฌ์ธํฐ๋ฅผ ์ฆ๊ฐ์ํค๋ฉด ๋๋ ๋จ์ํ ๊ตฌ์กฐ์ด๋ค

Heap

- Heap์ ํฌ๊ธฐ๊ฐ ๋ฐํ์์ ๊ฒฐ์ ๋๋ค
- Heap์ ๋ฉ๋ชจ๋ฆฌ์ ํ ๋นํ๊ธฐ ์ํด ์ฌ์ฉํ์ง ์์ ๋น ๋ฉ๋ชจ๋ฆฌ ๋ธ๋ก์ ์ฐพ์์ผ ํ๋ ๋น์ฉ์ด ์๋ค. ๋ํ ํด์ ๋ฅผ ์ํด ๋ฉ๋ชจ๋ฆฌ ๋ธ๋ก์ ๋ค์ ์ฝ์ํด์ผ ํ๋ ๊ณผ์ ์ด ์๋ค.
- ๋์ ์ผ๋ก ํ ๋น/ํด์ ๊ฐ ๋๊ธฐ ๋๋ฌธ์ thread-safe๋ฅผ ๋ณด์ฅํ๊ธฐ ์ํ ์ค๋ฒํค๋๊ฐ ์กด์ฌํ๋ค

### Struct์ Class์ Allocation

#### Struct
![](https://i.imgur.com/fMLxZqv.png)

Point๋ผ๋ ๊ตฌ์กฐ์ฒด๊ฐ ์๊ณ , point1์ point2์ ํ ๋นํ๋ฉด ๋ณต์ฌ๋ ๋ชจ์ต์ ๋ณผ ์ ์๋ค.
๊ฐ์ ์ธ์คํด์ค๊ฐ ์๋๊ณ  ๊ฐ๋ง ๊ฐ์ ๋ณต์ฌ๋ ์ธ์คํด์ค์ด๊ธฐ ๋๋ฌธ์ 

![](https://i.imgur.com/VLfO2dQ.png)

point2์ x์ ๊ฐ์ ๋ฐ๊ฟจ์ ๋ point1์๋ ์ํฅ์ด ์๋ ๊ฒ์ ๋ณผ ์ ์๋ค.

#### Class

๋๊ฐ์ Point๊ฐ class๋ก ๊ตฌํ๋์ด ์๋ค.
์ฐธ์กฐ ํ์์ ๋ฐ์ดํฐ๋ Heap ์์ญ์ ํ ๋น๋์ง๋ง, Stack์๋ Heap์ ๊ฐ๋ฆฌํค๋ ์ฃผ์๊ฐ์ ์ ์ฅํ๋ค.

point1์ point2์ ํ ๋น์ฃผ๋ฉด ์ฐธ์กฐ ํ์์ด๊ธฐ ๋๋ฌธ์ ๋์ผํ heap ๋ฉ๋ชจ๋ฆฌ ์ฃผ์๋ฅผ ๊ฐ๋ฆฌํค๊ฒ ๋๋ค.

![](https://i.imgur.com/2VzYMqZ.png)

๊ทธ๋์ point2์ x๊ฐ์ 5๋ก ๋ฐ๊พธ๋ฉด point1์ x๋ 5๋ก ๋ฐ๋๊ฒ ๋๋ค.

struct์ class์ ์ฑ๋ฅ์ ๊ทธ๋ฆผ์ผ๋ก ๋น๊ตํด๋ณด๋ฉด ์๋์ ๊ฐ๋ค

![](https://i.imgur.com/Zva0Djm.png)

![](https://i.imgur.com/uIBU3xH.png)

์ ๊ทธ๋ฆผ์ ๋ณด๋ฉด struct๊ฐ class๋ณด๋ค ์ฑ๋ฅ์ด ๋ฐ์ด๋๋ฐ, ์ด๋ป๊ฒ ๊ฐ์ ์ ํ  ์ ์์๊น?

![](https://i.imgur.com/NvZk9dp.png)

๋์๋๋ฆฌ ํ์์ธ cache์ ํค๋ String ํ์์ด๋ค.
String์ struct๋ก ๊ตฌํ๋์ด ์์ง๋ง, ํฌ๊ธฐ๊ฐ ๋์ ์ผ๋ก ๋ณํ๊ธฐ ๋๋ฌธ์ ์ปดํ์ผ ํ์์ ๊ทธ ํฌ๊ธฐ๋ฅผ ์ ์ ์์ด heap ์์ญ์ ์ฌ์ฉํ๋ค.
์ด๋ String์ ์ฌ์ฉํ๋ ๊ฒ์ด ์๋๋ผ, ํ์์ ๋ง๋ค์ด์ ํด๊ฒฐํด๋ณผ ์ ์๋ค.

![](https://i.imgur.com/OPX51Au.png)

enum์ผ๋ก ๊ตฌํ๋์ด ์๋ ํ๋กํผํฐ๋ก ๊ตฌ์ฑ๋ struct๋ฅผ ๋ง๋ค์ด key๋ก ํ์ฉํด String ์ฌ์ฉ์ ๊ฐ์ ํ  ์ ์๋ค.

## ๐ Reference Counting

### Class

![](https://i.imgur.com/8jk7EK4.png)

์ธ์คํด์ค๋ฅผ ํ ๋นํ๊ณ , ์ฌ์ฉ์ ์๋ฃํ๋ ์ฌ์ด์ retain๊ณผ release ์ฝ๋๊ฐ ํธ์ถ๋๋ ๊ฒ์ ๋ณผ ์ ์๊ณ , Point์ ํ๋กํผํฐ๋ก refCount๊ฐ ํ๋กํผํฐ๋ก ์กด์ฌ ํ๋ค.
point1์ point2์ ํ ๋นํ๋ฉด, retain์ด ์คํ๋๊ธฐ ๋๋ฌธ์ refCount๋ 1์ด ๋ํด์ ธ 2๊ฐ ๋๋ค.

![](https://i.imgur.com/5XZuyBw.png)

point1๊ณผ point2๊ฐ ์ฌ์ฉ์ ๋ง์น๊ณ  refCount๊ฐ 0์ด ๋๋ฉด ,
๋ ์ด์ ํ์ํ์ง ์์ ๊ฒ์ผ๋ก ์ธ์ํ๊ณ  ๋ฉ๋ชจ๋ฆฌ์์ ํด์ ํ๋ค.
![](https://i.imgur.com/ooRiMwE.png)

stack๊ณผ heap ์์ญ์์ ๋ชจ๋ ๋ฉ๋ชจ๋ฆฌ ํด์ ๋๋ค.

### Struct

struct๋ ๊ธฐ๋ณธ์ ์ผ๋ก ์ฐธ์กฐ๋ฅผ ๊ฐ์ง์ง ์๊ธฐ ๋๋ฌธ์ ์ฐธ์กฐ ์นด์ดํธ๋ ๊ฐ์ง์ง ์์ง๋ง, struct๊ฐ ๋ด๋ถ์ ์ผ๋ก ์ฐธ์กฐ ํ์์ ๊ฐ์ง๋ ๊ฒฝ์ฐ์๋ ์ฐธ์กฐ ์นด์ดํธ๋ฅผ ๋ณด์ ํ๊ฒ ๋๋ค.
![](https://i.imgur.com/svl3L2S.png)

์ Label์ struct ์ด์ง๋ง, text๋ String ํ์์ด๋ฉฐ (heap์ ํ ๋น), UIFont๋ ํด๋์ค์ด๊ธฐ ๋๋ฌธ์ reference๋ฅผ ๊ฐ์ง๊ฒ ๋๋ค.
๊ทธ๋์ Label์ ์ธ์คํด์ค๋ฅผ ์์ฑํ๋ ์์ ์ ์ฐธ์กฐ๊ฐ ํ๋ ์๊ธฐ๊ฒ ๋๊ณ , 

![](https://i.imgur.com/YJ8nMLM.png)

label1์ ๋ณต์ฌํด์ label2 ์ธ์คํด์ค๋ฅผ ๋ง๋ค ๋์๋ ์ด์ ์ ์์ฑ๋ ์ฃผ์๋ฅผ ๊ฐ๋ฆฌํค๋ ์ฐธ์กฐ๊ฐ ์๊ธฐ๊ฒ ๋๋ค.
label1์ ๋ณต์ฌํ๋ฉด ํ๋กํผํฐ ๋ ๊ฐ ๋ชจ๋ reference count๊ฐ ์ฆ๊ฐํ๊ฒ ๋๋ค. 
๊ทธ๋ ๊ธฐ ๋๋ฌธ์ struct ๋ด์ ์ฐธ์กฐ ํ์์ด ์กด์ฌํ  ๊ฒฝ์ฐ, ์ฐธ์กฐ ํ์์ ํ๋กํผํฐ๊ฐ ๋ง์ ์๋ก ์ฑ๋ฅ์ด ๋จ์ด์ง๋ค.

#### ๐ง ์์: ๊ตฌ์กฐ์ฒด ํ๋กํผํฐ๊ฐ RC ๋ฐ์์ํค๋ฉด ์ด๋ป๊ฒ ํด๊ฒฐ?
mimetype: ex. png, jpg
* ๋ฌธ์ 
![](https://i.imgur.com/FsII0Y5.png)

* ํด๊ฒฐ
![](https://i.imgur.com/D2YJzNA.png)

UUID๋ 128bit random ID์ธ๋ฐ, ๊ตฌ์กฐ์ฒด์ ๋ฐ๋ก ์ ์ฅํ  ์ ์๋ค.
์ด๋ฅผ ํตํด String๋ณด๋ค type safety(์ง UUID๋ง, ID์ ์๋ฏธ๊ฐ ์๋ ๊ฒ๋ง ๋ค์ด์ด)๊ฐ ๋ณด์ฅ๋๋ค.

indirectly store in heap! ํด๊ฒฐ: enum ํ์
![](https://i.imgur.com/ghdbDcF.png)


![](https://i.imgur.com/TGxJHLI.png)

![](https://i.imgur.com/M2Azq8p.png)

## ๐ Method Dispatch

### Static Method Dispatch

์ด๋ค ๋ฉ์๋ ๊ตฌํ๋ถ๋ฅผ ์คํํด์ผ ํ๋์ง ์๊ณ  ์๊ธฐ ๋๋ฌธ์, ๋ฐํ์์ ๋ฐ๋ก ๊ตฌํ๋ถ๋ก ์ด๋ํ  ์ ์๋ค.
์ธ๋ผ์ด๋์ผ๋ก ์ปดํ์ผ๋ฌ์ ์ต์ ํ๊ฐ ๊ฐ๋ฅํ๋ค.
![](https://i.imgur.com/Zjb8NZ7.png)

![](https://i.imgur.com/aSymqaB.png)

drawAPoint๋ฅผ ์คํํ  ๋ Point.draw()์ ๊ตฌํ๋ถ๋ก ๋์ฒดํ  ์ ์๋ค.

### Dynamic Method Dispatch

๋ฐํ์๋ง๋ค v-table์ ํตํด ๋ฉ์๋ ๊ตฌํ๋ถ๋ฅผ ์ฐพ๊ฒ ๋๋ค. ๊ทธ ์ดํ ๊ตฌํ๋ถ๋ก ์ด๋ํ์ฌ ๋ฉ์๋๋ฅผ ์คํํ๊ฒ ๋๋ค. ๊ทธ๋ ๊ธฐ ๋๋ฌธ์ ์ธ๋ผ์ด๋์ด ์ด๋ ค์์ง๋ค.
=> private, final ํค์๋๋ฅผ ์ด์ฉํ์ฌ ์์์ด ๋์ง ์์์ ๋ํ๋ด๋ฉด static dispatch๋ก ์คํ๋  ์๋ ์๋ค.

๊ทธ๋ผ Dynamic Dispatch๋ฅผ ์ฌ์ฉํ๋ ์ด์ ๋?? ๋คํ์ฑ (Polymorphism) ๋๋ฌธ

V-Table์ ์์ง ์์ ๊ด๊ณ๋ฅผ ํฌํจํ๋ ํ์ด๋ธ๋ก, ๋ฉ์๋ ์ค๋ฒ๋ผ์ด๋ฉ์ ๋ฐ๋ผ ์คํ ์์ ์ ์ด๋ค ๋ฉ์๋๋ฅผ ์คํํ ์ง ๊ฒฐ์ ํ๋ dynamic dispatch๋ฅผ ์ง์ํ๊ธฐ ์ํด ์ฌ์ฉ๋๋ ๋ฉ์ปค๋์ฆ์ด๋ค.

์์์ ํ๋ฉด ๋ฉ์๋๋ฅผ ์ฌ์ ์ํ  ์ ์๊ฒ ๋๋๋ฐ, ์ด๋ ๊ฐ ๋ฉ์๋์ ๊ตฌํ๋ถ๊ฐ ๋ฌ๋ผ์ง๊ธฐ ๋๋ฌธ์ ์ปดํ์ผ๋ฌ๋ ์ด๋ค ๋ฉ์๋์ ๊ตฌํ๋ถ๋ก ์ด๋ํด์ผ ํ๋์ง ๋ชจ๋ฅด๋ ์ํฉ์ด ์จ๋ค.
![](https://i.imgur.com/ifUK4gf.png)

![](https://i.imgur.com/xgOhWwI.png)

d.draw()์์ Line์ draw()๋ฅผ ์คํํด์ผ ํ๋์ง, Point์ draw()๋ฅผ ์คํํด์ผ ํ๋์ง ์ ์ ์์ ๋, ์ปดํ์ผ๋ฌ๋ ์ด๋ค draw๋ฅผ ํธ์ถํด์ผ ํ๋์ง ์ด๋ป๊ฒ ๊ฒฐ์ ํ ๊น?
์ปดํ์ผ๋ฌ๋ ๊ทธ class ํ์์ ์ ๋ณด๋ฅผ static memory์ ์ ์ฅํ๊ณ , ์ค์ ๋ก draw๋ฅผ ํธ์ถํ  ๋ vtable์ ์กฐํํ๋ค.
๊ทธ๋ฆฌ๊ณ  ์คํํ๊ธฐ์ ์ ํฉํ draw() ๋ฉ์๋๋ฅผ ์ฐพ๊ณ , ํ๋ผ๋ฏธํฐ๋ก ์ค์  ์ธ์คํด์ค๋ฅผ ์ ๋ฌํ๋ค.

## ๐ Protocol Types

### ์์์ด๋ reference semantics๊ฐ ์๋ ๋คํ์ฑ (Struct + Protocol)

![](https://i.imgur.com/IKrnHtE.png)

Point์ Line์ ๊ตฌ์กฐ์ฒด์ด๊ธฐ ๋๋ฌธ์ ๊ณตํต ์์ ๊ด๊ณ๊ฐ ์๋๋ค.
๋ฐ๋ผ์ vtable์ ์กฐํํ์ง ์๋๋ฐ, ์ปดํ์ผ ์์ ์ ์ด๋ค draw()๊ฐ ํธ์ถ๋์ด์ผ ํ ์ง๋ ๋ชจ๋ฅด๋ ์ํฉ.
=> Protocol Witness Table

pwt์ ์ํธ๋ฆฌ๋ ํด๋น ํ์์ ๊ตฌํ์ ์ฐ๊ฒฐ(link)๋๋ค.
![](https://i.imgur.com/rKwmZ1X.png)

๊ทธ๋ฐ๋ฐ [Drawable] ๋ฐฐ์ด์์ ์ด๋ป๊ฒ pwt ํ์ด๋ธ๋ก ์ด๋ํ  ์ ์์๊น?

![](https://i.imgur.com/QShQbNj.png)

Point๋ ํ๋กํผํฐ๊ฐ 2๊ฐ, Line์ ํ๋กํผํฐ๊ฐ 4๊ฐ ์์ด์ ์ฌ์ด์ฆ๊ฐ ๋ค๋ฅธ๋ฐ,
๋ฐฐ์ด์ ๊ณ ์ ๋ offset์ ์์๋ฅผ ์ ์ฅํ๋ ค๊ณ  ํ๋ค.
์ด๋ด ๋ Swift๋ Existential Container๋ผ๋ ํน์ํ storage layout์ ์ฌ์ฉํ๋ค.

### Existential Container

![](https://i.imgur.com/r3A2agY.png)

Existential Container๋ valueBuffer + vwt + pwt๋ก ๋์ด ์๋ค.
Existential Container์ ์ฒ์์๋ 3๊ฐ๊ฐ valueBuffer์ฉ์ผ๋ก ์์ฝ ๋์ด ์๋ค.

๊ทธ๋์ Point์ ๊ฐ์ด ๊ฐ์ด 2๊ฐ๋ง ํ์ํ๋ฉด
![](https://i.imgur.com/y1Od8aR.png)

valueBuffer์ ๋ค์ด๊ฐ ์ ์๋ค.

ํ์ง๋ง Line์ ๊ฐ์ด 4๊ฐ๊ฐ ํ์ํ๊ณ , ์ด๋ฐ ํฐ ํ์์ Heap์ ๋ฉ๋ชจ๋ฆฌ๋ฅผ ํ ๋นํ๊ณ , ํด๋น ๋ฉ๋ชจ๋ฆฌ์ ๋ํ ํฌ์ธํฐ๋ฅผ Existential Container์ ์ ์ฅํ๋ค.

![](https://i.imgur.com/qBR0v2A.png)

์ด๋ ๊ฒ ๋๋ฉด Point์ Line์ด ๋ค๋ฅธ ๋ฐฉ์์ผ๋ก ์ ์ฅ๋๊ธฐ ๋๋ฌธ์ Existential Container๋ ์ด ์ฐจ์ด๋ฅผ ๊ด๋ฆฌํ  ํ์๊ฐ ์๋ค.
=> VWT (Value Witness Table)

### Value Witness Table (VWT)

: value์ lifetime์ ๊ด๋ฆฌํ๋ฉฐ, ํ์๋ง๋ค Value Witness Table์ ๊ฐ๊ณ  ์๋ค.
(Point๋, Line๋ VWT๊ฐ ์๋ค)

1๏ธโฃ allocate
![](https://i.imgur.com/CZllRb5.png)

ํ๋กํ ์ฝ ํ์์ ๋ก์ปฌ ๋ณ์์ lifetime์ด ์์๋  ๋, Swift๋ VWT์ allocate ํจ์๋ฅผ ํธ์ถํ๋ค
(Line์ Existential Container์ ๋ค์ด๋ง์ง ์๊ธฐ ๋๋ฌธ์ Heap ๋ฉ๋ชจ๋ฆฌ ํ ๋น ํ์)
allocate ํจ์ ์์์ Heap์ ๋ฉ๋ชจ๋ฆฌ๋ฅผ ํ ๋นํ๊ณ , ํด๋น ๋ฉ๋ชจ๋ฆฌ์ ๋ํ ํฌ์ธํฐ๋ฅผ Existential Container์ valueBuffer ๋ด์ ์ ์ฅํ๋ค.

2๏ธโฃ copy
![](https://i.imgur.com/ftXOE9U.png)

๋ก์ปฌ ๋ณ์๋ฅผ ์ด๊ธฐํ ํ๋ ์์ค์์ Existential Container๋ก ๊ฐ์ ๋ณต์ฌ ํด์ผํ๋ค.
(Point์ ๊ฐ์ด valueBuffer์ ๋ค์ด๋ง๋ ํ์์ valueBuffer ๋ด์ ๊ฐ์ ์ ์ฅํ๊ธฐ ๋๋ฌธ์, copy ๋ถ๋ถ์์ Existential Container๋ก ๊ฐ์ ๋ณต์ฌํจ)
Line์ valueBuffer์ ๋ค์ด๋ง์ง ์๊ธฐ ๋๋ฌธ์ copy์์ Heap์ ๊ฐ์ด ๋ณต์ฌ๋๋ค.

3๏ธโฃ destruct
![](https://i.imgur.com/cIYOHjS.png)

local ๋ณ์๋ฅผ ๋ค ์จ์ lifetime์ด ๋๋๋ฉด Swift๋ VWT์์ destruct๋ฅผ ํธ์ถํ๋ค.
destruct๋ ๊ฐ์ ๋ํ ๋ ํผ๋ฐ์ค ์นด์ดํธ๋ฅผ ๊ฐ์์ํจ๋ค.

4๏ธโฃ deallocate
![](https://i.imgur.com/RkoFRno.png)

๊ทธ๋ฆฌ๊ณ  deallocate์ ํธ์ถํ์ฌ Line์ด Heap ๋ฉ๋ชจ๋ฆฌ ํ ๋น์ ํด์ ํ๋ค.

vwt์์๋ ์ ์ฅ ํ๋กํผํฐ๋ฅผ ๊ด๋ฆฌํ๊ณ , pwt์์๋ ํ๋กํ ์ฝ ๋ฉ์๋๋ฅผ ๊ด๋ฆฌํ๋ค.

*** Swift๋ existential container ํ๋์์ pwt๋ฅผ ์กฐํํ๊ณ , ํด๋น table์ fixed offset์ ์๋ ๋ฉ์๋๋ฅผ ์กฐํํ์ฌ ๊ตฌํ์ผ๋ก ์ด๋ํ๋ค ***

## ๐ Generic Code

Generic Code๋ staticํ ํํ์ polymorphism์ ์ง์ํ๋ค.
staticํ ํํ์ polymorphism์ Generic์ specialization์ด๋ผ๊ณ  ๋ถ๋ฆฌ๋ ์ปดํ์ผ๋ฌ ์ต์ ํ๋ฅผ ๊ฐ๋ฅํ๊ฒ ํ๋ค.
![](https://i.imgur.com/IULWU4V.png)

drawACopy์์ Point๋ฅผ ํ๋ผ๋ฏธํฐ๋ก ํธ์ถํ๋ฉด
Swift๋ ํด๋น ํ์์ ์ฌ์ฉํ์ฌ ํจ์์์ Generic ๋งค๊ฐ๋ณ์๋ฅผ ๋์ฒดํ๊ณ , ํด๋น ํ์ ๋ฒ์ ์ ํจ์๋ฅผ ๋ง๋ ๋ค.
Line์ ํ๋ผ๋ฏธํฐ๋ก ํธ์ถํ๋ฉด Line ๋ฒ์ ์ drawACopy๊ฐ ์๊ธด๋ค.

![](https://i.imgur.com/240i9jq.png)

ํ์๋ง๋ค ํด๋น ํจ์์ ๋ฒ์ ์ ๋ง๋ค๊ธฐ ๋๋ฌธ์ code size๋ฅผ ์ฆ๊ฐ์ํฌ ๊ฐ๋ฅ์ฑ์ด ์๋ค.
ํ์ง๋ง aggressive compiler optimization์ด ๊ฐ๋ฅํ๊ธฐ ๋๋ฌธ์ ์ค์ ๋ก๋ Swift๊ฐ code size๋ฅผ ์ค์ผ ์ ์๋ค.
specialization์ ํตํด existential container๊ฐ ํ์ ์์ด์ง๊ณ  (ํด๋น ํ์ ๋ฒ์ ์ผ๋ก ํจ์๋ฅผ ๋ง๋ค๊ธฐ ๋๋ฌธ์, ๊ตณ์ด ํด๋น ๋ฉ์๋๊ฐ ๋๊ตฌ๊ป์ง ์ ์ฅํ  pwt๊ฐ ํ์ ์์ด์ง๊ธฐ ๋๋ฌธ),
static dispatch๊ฐ ๊ฐ๋ฅํด์ง๋ค.

์ต์ํ์ dynamic ๋ฐํ์ ์๊ตฌ์ฌํญ์ผ๋ก ์ ํฉํ ์ถ์ํ ์ ํ
- struct types: value semantics    
- class types: identity or OOP style polymorphism
- Generics: static polymorphism
- Protocol types: dynamic polymorphism
- ํฐ ๊ฐ๋ค์ ์ฒ๋ฆฌํ  ๋๋ indirect storage ์ฌ์ฉ





