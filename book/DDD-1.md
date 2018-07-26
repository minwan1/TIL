## 도메인


## 요구사항분석

1. 유저는 여러개의 주문을 할 수 있다. 이주문에는 오덜넘버와 총 금액이 들어있다.
2. 배송전에 주문을 취소할 수 있다.
3. 




문제를 해결해야하는 대상 도메인이다. 도메인 모델은 특정 도메인을 개념으로 표현한것이다. 


도메인 계층은 도메인의 핵심 규칙을 구현한다.
출고전에 배송지를 변경할수 있다는 규칙과 주문 취소는 배송전에만 할 수 있다는 구현할 코드가 도메인 계층에 위치하게된다.



```java



public enum OrderState{
    // 주문정보가 변경 가능한지 체크해준다.
    //주문이 가능한지 체크해주는 로직


    PAYMENT_WAITTING{

        public boolean isShppingChangeable(){ // 이때는 가능함
            return true;
        }
    },

    PREPARING{
        public boolean isShipingChangeable(){// 이떄도 가능함
            return true;
        }
    },

    SHIPPED, DELIVERELING, DELIVERY_COMPLETED; // 이떄불가

    public boolean isShippingChangeable(){
        return false;
    }

}
```


* 주문 변경 가능여부를 OrderState -> order로 변경을했는데, 여기에서 중요한점은  배송지 변경 가능여부를 판단하는 기능이 Order에 있든, OrderState에 있든 중요한 점은 주문과 관련된 중요 업무 규칙을 주문 도메인모델인 Order나 OrderState에서하는게 중요하다.