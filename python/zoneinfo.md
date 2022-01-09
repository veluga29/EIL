# Python `zoneinfo` - UTC 시간대를 더욱 쉽게 적용합시다!

이전에 python에서 UTC 시간대를 적용할 때는 `pytz` 라이브러리가 주로 사용되었습니다.

특히 aware 타입과 naive 타입을 비교하기 어렵기 때문에, `pytz`를 사용해 `datetime` 객체를 aware 타입으로 바꾸고 비교하는 것은 매우 유용했습니다. (aware 타입은 timezone 정보가 포함된 `datetime`이고 naive 타입은 timezone 정보가 포함되지 않은 `datetime`입니다.)

그러나 `pytz`는 2018년 서울과 평양시간을 UTC+9 시간이 아닌 UTC+08:30으로 표현하는 버그, 실수를 유발할 수 있는 사용 방법 등 이슈도 공존했습니다. 이를 보완하기 위해, Python은 3.9 버전부터 표준 라이브러리로 `zoneinfo` 모듈을 제공합니다. 덕분에, 따로 `pytz`를 인스톨하지 않고도 `datetime`에 쉽게 원하는 시간대를 적용할 수 있습니다.

​    

> Python official document - zoneinfo
>
> https://docs.python.org/ko/3/library/zoneinfo.html

​    

## `ZoneInfo` 클래스

```python
ZoneInfo(key: str)
```

`ZoneInfo`는 key를 생성자의 인자로 받는 클래스입니다. 예를 들어, "America/New_York", "Europe/London"를 key로 던지면, 해당 시간대 정보를 가지는 인스턴스를 생성합니다. 시간대 적용은 이 인스턴스를 활용합니다.

​    

## 현재 시간에 `ZoneInfo` 적용하기

```python
from zoneinfo import ZoneInfo
from datetime import datetime

dt = datetime.now(ZoneInfo('UTC'))
# datetime.now(tz=ZoneInfo('UTC'))와 동일
print(dt)

# 2022-01-09 11:05:40.133971+00:00
```

`zoneinfo`는 기존 `datetime` 객체에 그대로 적용할 수 있습니다. UTC 시간대를 적용한 현재 시간을 알고 싶다면, `datetime.now(ZoneInfo('UTC'))`을 사용합니다.(`datetime.now(tz=ZoneInfo('UTC'))`와 동일합니다.) 반환된 `dt`는 aware 타입 객체가 될 것입니다.

```python
dt = datetime.now(ZoneInfo('Asia/Seoul'))
print(dt)

# 2022-01-09 20:05:40.133971+09:00
```

서울의 시간대로 현재 시간을 알고 싶다면, `ZoneInfo`의 인자로 'Asia/Seoul' key를 적용합니다.

​    

## 임의의 `datetime`에 `ZoneInfo` 적용하기

```python
from zoneinfo import ZoneInfo
from datetime import datetime, timedelta

dt = datetime(2020, 10, 31, 12, tzinfo=ZoneInfo("America/Los_Angeles"))
print(dt)

# 2020-10-31 12:00:00-07:00
```

원하는 시간에 `ZoneInfo`를 적용하고 싶다면, `datetime`의 `tzinfo`에 `ZoneInfo` 정보를 줍시다.

```python
dt_add = dt + timedelta(days=1)
print(dt_add)

# 2020-11-01 12:00:00-08:00
```

`datetime`끼리의 연산 역시 summer time을 고려해 알아서 계산됩니다.

​    

## Windows와 tzdata

`zoneinfo`는 Python의 표준 라이브러리에 포함되기 때문에, 따로 인스톨없이 사용할 수 있습니다.

다만, 윈도우의 경우 `zoneinfo` 모듈을 사용할 때 다음과 같은 에러가 발생할 수 있습니다.

> ModuleNotFoundError: No module named 'tzdata'

​    

`zoneinfo`는 기본적으로 시스템의 시간대 데이터를 사용합니다. 하지만, 윈도우는 시간대를 다루는 시스템이 다른 OS와 조금 달라서, `zoneinfo`와 호환되지 않는다고 합니다. (PEP 615)

> However, not all systems ship a publicly accessible time zone database — notably Windows uses a different system for managing time zones — and so if available `zoneinfo` falls back to an installable first-party package, `tzdata`, available on PyPI. [[d\]](https://www.python.org/dev/peps/pep-0615/#d) If no system zoneinfo files are found but `tzdata` is installed, the primary `ZoneInfo` constructor will use `tzdata` as the time zone source. - [Sources for time zone data](https://www.python.org/dev/peps/pep-0615/#sources-for-time-zone-data) (PEP 615)

​    

이 때는, CPython 핵심 개발자가 유지 보수하는 first-party 패키지인 `tzdata`를 인스톨합시다. (`pip install tzdata`) 

`zoneinfo`는 참고할 수 있는 시간대 데이터가 없을 시 자동으로 `tzdata`를 시간대 데이터로 사용하므로, 인스톨 시 문제가 해결됩니다.

​    

개발 시 최대한 신뢰할 수 있는 라이브러리를 사용하고 이외의 라이브러리에 대한 의존성을 줄일 필요가 있습니다. 고마웠던 `pytz`지만, 가능하다면 표준 라이브러리에 포함된 `zoneinfo` 사용을 지향해봐야겠습니다.

​    

## Reference

[Python 3.10 document - zoneinfo](https://docs.python.org/ko/3/library/zoneinfo.html)

[PEP 615 - Support for the IANA Time Zone Database in the Standard Library](https://www.python.org/dev/peps/pep-0615/)

[PYTHON 3.9에 등장한 상큼한 8가지 FEATURES](https://tech.madup.com/Python3.9/)

[평양 및 서울의 timezone관련 pytz 이슈](https://github.com/stub42/pytz/issues/15)