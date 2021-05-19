# Is there any point of "reference of pointer"?

포인터 값의 참조를 하는 것이 의미가 있는것인지 궁금해졌다.

아래의 코드를 작성하던 중이었다.

```c++
vector<Figure*> vFigureList;

//auto&
for(auto& pFigure : vFigureList)
{
    pFigure->draw();
    delete pFigure;
}

//auto
for(auto pFigure : vFigureList)
{
    pFigure->draw();
    delete pFigure;
}

//Figure*
for(Figure* pFigure : vFigureList)
{
    pFigure->draw();
    delete pFigure;
}

//Figure*&
for(Figure*& pFigure : vFigureList)
{
    pFigure->draw();
    delete pFigure;
}
```

위의 네가지 경우가 생각났는데,

예상컨데,

auto& 의 경우는 Figure*& 의 경우와 똑같이 동작할 것이고,

auto 의 경우는 Figure* 의 경우와 똑같이 동작할 것이다.

위 네가지 경우 모두 잘 동작하는것을 확인했다.

1. 성능상의 차이가 있는가?
2. 어셈블리 레벨에서의 차이가 있는가?
3. 위의 코드들에게 근본적인 차이가 있는가?

사실 위 세가지 질문이 거의 같은 질문인 것 같다.

---

한가지 알 수 있는 차이는

```c++
for(auto& pFigure : vFigureList)
{
    delete pFigure;
    pFigure = nullptr;
}

for(auto pFigure : vFigureList)
{
    delete pFigure;
    pFigure = nullptr; // 백터의 내용에는 아무 영향이 없다
}
```

다음처럼 loop 안에서 백터의 내용물을 변경할때는 차이가 분명하다.

그렇다는 것은, 아마 어셈블리 레벨에서도 차이가 있을 것 같다.

-2021.05.19-

---

