# RE(regular expression) & ply



## 정규 표현식(RE)

정규 표현식(Regular Expressions)은 복잡한 문자열을 처리할 때 사용하는 기법으로, 파이썬만의 고유 문법이 아니라 문자열을 처리하는 모든 곳에서 사용한다.

```python
주민등록번호를 포함하고 있는 텍스트가 있다. 이 텍스트에 포함된 모든 주민등록번호의 뒷자리를 * 문자로 변경해 보자.
```

- 정규표현식을 사용하지 않은 코드

```python
data = """
park 800905-1049118
kim  700905-1059119
"""

result = []
for line in data.split("\n"):
    word_result = []
    for word in line.split(" "):
        if len(word) == 14 and word[:6].isdigit() and word[7:].isdigit():
            word = word[:6] + "-" + "*******"
        word_result.append(word)
    result.append(" ".join(word_result))
print("\n".join(result))
```

```
결과값:
park 800905-*******
kim  700905-*******
```

- 정규표현식을 사용한 코드

```python
import re 

data = """
park 800905-1049118
kim  700905-1059119
"""

pat = re.compile("(\d{6})[-]\d{7}")
print(pat.sub("\g<1>-*******", data))
```

```
결과값:
park 800905-*******
kim  700905-*******
```



### 정규표현식(RE)의 기초, 메타문자

메타 문자란 원래 그 문자가 가진 뜻이 아닌 특별한 용도로 사용하는 문자를 말한다.

```c
. ^ $ * + ? { } [ ] \ | ( )
```

- 정규 표현식에 위 메타 문자를 사용하면 특별한 의미를 갖게 된다.



### 문자 클래스(character class) [   ]

"[ ] 사이의 문자들과 매치" 를 의미한다.

문자 클래스를 만드는 메타 문자인 [ ] 사이에는 어떤 문자도 들어갈 수 있다.

즉 정규 표현식이 [abc]라면 이 표현식의 의미는 "a, b, c 중 한 개의 문자와 매치"를 뜻한다.

> "a"는 정규식과 일치하는 문자인 "a"가 있으므로 매치
>
> "before"는 정규식과 일치하는 문자인 "b"가 있으므로 매치
>
> "dude"는 정규식과 일치하는 문자인 a, b, c 중 어느 하나도 포함하고 있지 않으므로 매치되지 않음

[  ] 안의 두 문자 사이에 하이픈(-)을 이용하면 두문자 사이의 범위(From - To)를 의미한다

- ex) [a-c]라는 정규식 표현은 [abc]와 동일하다.
- ex) [a-zA-Z] : 알파벳 모두
- ex) [0-9] : 숫자

[   ] 안에는 어떤 문자나 메타 문자도 사용할수 있다 하지만 (**^**) 메타문자는 **반대**라는 의미를 가는다. <u>[^0-9]</u> 는 숫자가 아닌 문자만 매치된다.

> 위의방식과는 다르게 자주사용하는 별도의 표기법도 존재한다
>
> \d - 숫자와 매치한다, [0-9]와 동일한 표현식이다.
>
> \D - 숫자가 아닌 것과 매치,  <u>[^0-9]</u>과 동일한 표현식이다.
>
> \s - whitespace 문자와 매치, [ \t\n\r\f\v]와 동일한 표현식이다. 맨 앞의 빈 칸은 공백문자(space)를 의미한다.
>
> \S` - whitespace 문자가 아닌 것과 매치, `<u>[^ \t\n\r\f\v]</u>와 동일한 표현식이다.
>
> \w` - 문자+숫자(alphanumeric)와 매치, `[a-zA-Z0-9_]와 동일한 표현식이다.
>
> \W` - 문자+숫자(alphanumeric)가 아닌 문자와 매치, `<u>[^a-zA-Z0-9_]</u>와 동일한 표현식이다.



### Dot(.)

정규 표현식의 Dot(.) 메타문자는 줄바꿈 문자인 \n을 제외한 모든 문자와 매치됨을 의미한다.

정규식을 작성할 때 re.DOTALL 옵션을 주면 `\n` 문자와도 매치된다.

```
a.b
```

> "a + 모든문자 + b"

즉 a와 b라는 문자 사이에 어떤 문자가 들어가도 모두 매치된다는 의미이다.

>"aab"는 가운데 문자 "a"가 모든 문자를 의미하는 `.`과 일치하므로 정규식과 매치된다.
>
>"a0b"는 가운데 문자 "0"가 모든 문자를 의미하는 `.`과 일치하므로 정규식과 매치된다.
>
>"abc"는 "a"문자와 "b"문자 사이에 어떤 문자라도 하나는있어야 하는 이 정규식과 일치하지 않으므로 매치되지 않는다.



### 반복(*)

```
ca*t
```

-  `*`은 `*` 바로 앞에 있는 문자 a가 0부터 2억개까지 반복될 수 있다는 의미이다.

| 정규식 | 문자열 | Match 여부 | 설명                                    |
| ------ | ------ | ---------- | --------------------------------------- |
| ca*t   | ct     | YES        | "a"가 0번 반복되어 매치                 |
| ca*t   | cat    | YES        | "a"가 0번 이상 반복되어 매치 (1번 반복) |
| ca*t   | caaat  | YES        | "a"가 0번 이상 반복되어 매치 (3번 반복) |



### 반복(+)

```
ca+t
```

> "c + a(1번 이상 반복) + t"

| 정규식 | 문자열 | Match 여부 | 설명                                    |
| ------ | ------ | ---------- | --------------------------------------- |
| ca+t   | ct     | NO         | "a"가 0번 반복되어 매치                 |
| ca+t   | cat    | YES        | "a"가 0번 이상 반복되어 매치 (1번 반복) |
| ca+t   | caaat  | YES        | "a"가 0번 이상 반복되어 매치 (3번 반복) |



### 반복({m,n}, ?)

반복횟수를 3회만 또는 1회부터 3회까지만 제한하고 싶을때 쓸수 있는 방식이 존재한다.

{} 메타 문자를 사용하면 반복 횟수를 고정할 수 있다.

{m,n}을 사용하면 m부터 n까지 매치할 수 있다.

또한 {m,}처럼 사용하면 반복횟수가 3이상인 경우이고 {,3}처럼 사용하면 반복횟수가 3 이하를 의미한다.

> `{1,}`은 `+`와 동일하고, `{0,}`은 `*`와 동일하다.

1. {m}

```
ca{2}t
```

> "c + a(반드시 2번 반복) + t"

| 정규식 | 문자열 | Match 여부 | 설명                               |
| ------ | ------ | ---------- | ---------------------------------- |
| ca{2}t | cat    | NO         | "a"가 1번만 반복되어 매치되지 않음 |
| ca{2}t | caat   | YES        | "a"가 2번 반복되어 매치            |

2. {m,n}

```
ca{2,5}t
```

> "c + a(2~5회 반복) + t"

| 정규식     | 문자열  | Match 여부 | 설명                               |
| ---------- | ------- | ---------- | ---------------------------------- |
| `ca{2,5}t` | cat     | No         | "a"가 1번만 반복되어 매치되지 않음 |
| `ca{2,5}t` | caat    | Yes        | "a"가 2번 반복되어 매치            |
| `ca{2,5}t` | caaaaat | Yes        | "a"가 5번 반복되어 매치            |

3. ?

```
ab?c
```

> "a + b(있어도 되고 없어도 된다) + c"

| 정규식 | 문자열 | Match 여부 | 설명                    |
| ------ | ------ | ---------- | ----------------------- |
| `ab?c` | abc    | Yes        | "b"가 1번 사용되어 매치 |
| `ab?c` | ac     | Yes        | "b"가 0번 사용되어 매치 |

즉 b문자가 있거나 없거나 둘다 매치되는 경우이다.



### |

| 메타문자는 or 과 동일한 의미로 사용된다. A|B라는 정규식은 A 또는 B라는 의미이다.

```python
>>> p = re.compile('Crow|Servo')
>>> m = p.match('CrowHello')
>>> print(m)
<re.Match object; span=(0, 4), match='Crow'>
```



### ^

^ 메타문자는 문자열의 맨 처음과 일치함의 의미한다. 

```python
>>> print(re.search('^Life', 'Life is too short'))
<re.Match object; span=(0, 4), match='Life'>
>>> print(re.search('^Life', 'My Life'))
None
```

- ^Life 정규식은 Life문자열이 처음에 나오 경우에는 매치하지만 처음이 아니면 매치되지 않는다.



### $

`$` 메타 문자는 `^`메타 문자와 반대의 경우이다. 즉 `$`는 문자열의 끝과 매치함을 의미한다.

```python
>>> print(re.search('short$', 'Life is too short'))
<re.Match object; span=(12, 17), match='short'>
>>> print(re.search('short$', 'Life is too short, you need python'))
None
```

- short$ 정규식은 검색할 문자열이 short로 끝난 경우에는 매치되지만 그 이외의 경우에는 매치되지 않는다.

> `^` 또는 `$` 문자를 메타 문자가 아닌 문자 그 자체로 매치하고 싶은 경우에는 `\^`, `\$` 로 사용하면 된다.



### \A

`\A`는 문자열의 처음과 매치됨을 의미한다. `^`메타 문자와 동일한 의미이지만 `re.MULTILINE` 옵션을 사용할 경우에는 다르게 해석된다. 

`re.MULTILINE` 옵션을 사용할 경우 <u>`^`은 각 줄의 문자열의 처음과 매치</u>되지만 <u>`\A`는 줄과 상관없이 전체 문자열의 처음하고만 매치</u>된다.



### \Z

`\Z`는 문자열의 끝과 매치됨을 의미한다. 

이것 역시 `\A`와 동일하게 `re.MULTILINE` 옵션을 사용할 경우 `$` 메타 문자와는 달리 전체 문자열의 끝과 매치된다.



### \b

`\b`는 단어 구분자(Word boundary) 이다. 보통 단어는 whitespace에 의해 구분된다.

```python
>>> p = re.compile(r'\bclass\b')
>>> print(p.search('no class at all'))  
<re.Match object; span=(3, 8), match='class'>
```

> `\bclass\b` 정규식은 앞뒤가 whitespace로 구분된 class라는 단어와 매치됨을 의미한다. 따라서 no class at all의 class라는 단어와 매치됨을 확인할 수 있다.

```python
>>> print(p.search('the declassified algorithm'))
None
```

> 위 예의 the declassified algorithm 문자열 안에도 class 문자열이 포함되어 있긴 하지만 whitespace로 구분된 단어가 아니므로 매치되지 않는다.

```python
>>> print(p.search('one subclass is'))
None
```

> subclass 문자열 역시 class 앞에 sub 문자열이 더해져 있으므로 매치되지 않음을 알 수 있다.

- `\b` 메타 문자를 사용할 때 주의해야 할 점이 있다. `\b`는 파이썬 리터럴 규칙에 의하면 백스페이스(BackSpace)를 의미하므로 백스페이스가 아닌 단어 구분자임을 알려 주기 위해 `r'\bclass\b'`처럼 Raw string임을 알려주는 기호 r을 반드시 붙여 주어야 한다.



### \B

`\B` 메타 문자는 `\b` 메타 문자와 반대의 경우이다. 즉 whitespace로 구분된 단어가 아닌 경우에만 매치된다.

```python
>>> p = re.compile(r'\Bclass\B')
>>> print(p.search('no class at all'))  
None
>>> print(p.search('the declassified algorithm'))
<re.Match object; span=(6, 11), match='class'>
>>> print(p.search('one subclass is'))
None
```

>  class 단어의 앞뒤에 whitespace가 하나라도 있는 경우에는 매치가 안 되는 것을 확인할 수 있다.



## ply

