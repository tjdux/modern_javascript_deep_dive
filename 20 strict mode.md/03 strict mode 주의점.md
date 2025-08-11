## strict mode 주의점

### 전역에 strict mode를 적용하는 것은 지양

- 전역에 적용한 strict mode는 스크립트 단위로 적용됨
- strict mode 스크립트와 non-strict mode 스크립트를 혼용하는 것은 오류를 발생시킬 수 있음

### 함수 단위로 strict mode를 적용하는 것도 지양

- 어떤 함수는 strict mode를 적용하고 어떤 함수를 적용하지 않는 것은 바람직하지 않음
- 함수에 일일이 strict mode를 적용하는 것도 번거로움
- strict mode 함수가 참조할 외부 컨텍스트에 strict mode를 적용하지 않는다면 이 또한 문제가 발생할 수 있음
