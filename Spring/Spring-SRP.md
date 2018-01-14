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
