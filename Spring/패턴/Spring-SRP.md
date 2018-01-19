## Responsibility

service :   
클래스는 하나의 책임을 가져야한다. 만약 아래와같은 클래스가 있다고 해보자.

Class EmployeeImpl{
  calculatePay();
  save();
  describeEmployee();
}

그리고 여기에서 기능이 추가되었다고 가정해보자.

Class EmployeeImpl{
  calculatePay();
  save();
  describeEmployee() : String
  findById(id:Long) : Employee
}

## 개방 폐쇄 원칙(OPC) open-closed Principle
개방폐쇄원칙은 클래스나 모듈은 확장에는 열려있어야하고, 변경에는 닫혀있어야 한다. 쉽게 말해 인터페이스를 기반으로 인터페이스에 맞는 클래스(모듈)들을 확장을 해나갈 수 있는 구조를 만들되, 반면 그 해당 모듈(클래스)은 자ㅣㄴ이 변화가 불필요하게 일어나지 않도록 굳게 폐쇠되어 있어야한다.
