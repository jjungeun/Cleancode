# 방어적 프로그래밍

design by contract(계약에서 예외를 발생시키고 실패하게 되는 모든 조건을 기술)와 다르게 코드의 모든 부분을 유효하지 않은 것으로부터 스스로 보호할 수 있게 한다. 방어적 프로그래밍의 큰 주제는 다음 두가지이다.

- 시나리오의 오류를 처리하는 방법 : error handling

-  발생하지 않아야 하는 오류를 처리하는 방법 : assertion



### 에러 핸들링(error handling)

오류가 발생하기 쉬운 상황에서 이 프로시저를 사용한다. 일반적으로 데이터 입력 확인 시 자주 사용된다.

주요 목적은 예상되는 에러에 대해 실행을 계속 할지 아니면 중단할 지 결정하는 것이다. 에러를 처리하는 방법을 살펴보자.

- 값 대체

  오류로 인해 잘못된 값을 생성하거나 전체가 종료될 위험이 있을 경우 결과 값을 안전한 다른 값으로 대체하는 경우이다. 보다 안전한 값 대체 방법은 누락된 데이터에 기본 값을 사용하는 것이다.

- 예외 처리

  잘못되거나 누락된 데이터가 있는 경우 에러가 발생하기 쉽다는 가정으로 계속 실행하는 것보다 차라리 실행을 멈추는 것이 더 좋다. 함수는 심각한 오류에 대해 명확하게 알려주고 원래의 로직에 따라 흐름을 유지하는 것이 중요하다.

  예외는 보통 호출자에게 잘못을 알려주는데 함수에 예외가 너무 많으면 문맥이 자유롭지 않게된다. 함수가 응집력이 약하고 너무 많은 책임을 가지고 있으면 여러개의 작은 함수로 나눠야 한다는 신호일 수 있다.

  파이썬의 예외와 관련된 권장사항을 알아보자.

  - 올바른 수준의 추상화 단계에서 예외처리

    예외는 오직 한 가지 일을 하는 함수의 한 부분이어야 한다.  따라서 한 함수에서는 하나의 예외처리를 하는 것이 좋다.

  - Traceback 노출 금지

    파이썬에서 traceback은 매우 유용하고 많은 디버깅 정보를 포함하므로 중요한 정보를 공개하지 않도록 주의한다.

  - 비어있는 except 블록 지양

    이것은 파이썬의 안티패턴 중 안티패턴이다. 이 패턴은 너무 방어적이어서 조용히 오류를 지나쳐버린다. 파이썬은 매우 유연하여 결함있는 코드를 작성할 수 있다.

    ```
    try:
    	process()
    except:
    	pass
    ```

    이것은 절대 파이썬스러운 코드가 아니다. 이를 해결하기 위해 except문에서 오류를 처리하거나 그냥 except가 아닌 보다 구체적인 예외(AttributeError, ValueError, KeyError 등)를 사용하면 된다. 여기서 오류를 처리한다는 것은 예외상황을 로깅하거나 기본 값을 반환하는 것 등이 있다.

  - 원본 예외 포함

    오류 처리과정에서 또 오류가 발생하는 경우 메시지를 변경할 수도 있따. 파이썬 3에서는 ```raise <e> from <original_exception>```구문을 사용하면 된다. 예를 들어 기본 예외를 사용자 정의 예외로 래핑하고 싶다면 원래 예외를 다음처럼 포함할 수 있다.

    ```
    class InternalDataError(Exception):
    	"""데이터 예외"""
    
    def process(data_dict, record_id):
    	try:
    		return data_dict[record_id]
    	except KeyError as e:
    		raise InternalDataError("Record not present") from e
    ```

  

  ### Assertion

  어설션은 절대 일어나지 않아야 하는 상황에 사용되므로 assert 문에 사용된 표현식은 불가능한 조건을 의미한다. 이 상태가 된다는건 소프트웨어에 결함이 있음을 의미한다.

  어설션을 에러핸들링용도로 사용하는 것은 좋지 않다. 예를들어 다음상황은 바람직하지 않다.

  ```
  try:
  	assert condition.holds()
  except AssertionError:
  	alternative_procedure()
  ```

  어설션에 실패하면 반드시 프로그램을 종료시켜야 한다. 위의 코드에서 어설션 문장이 함수인 것도 문제이다. 함수 호출은 항사 반복가능하지는 않다. 보다 나은 방법은 코드를 줄이고 유용한 정보를 추가하는 것이다.

  ```
  result = condition.holds()
  assert result > 0, "에러 {0}".format(result)
  ```

  

  