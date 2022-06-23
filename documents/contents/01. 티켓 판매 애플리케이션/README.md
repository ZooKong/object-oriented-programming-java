# 티켓 판매 애플리케이션

#### 1. 티켓 판매 애플리케이션 개발

#### 1.1. 요구사항

극장이 있다.
극장에 입장하려는 관람객은 추첨을 통해 초대장을 받은 사람과 받지 못한 사람이 있다.
관람객을 입장시키기 전에 초대장을 갖고 있는지 확인해야 한다.
이를 위해서는 매표소가 있으며 매포소에는 판매원이 있다.
초대장이 있는 관람객은 초대장을 티켓으로 교환 후 입장할 수 있다.
반면에 초대장이 없는 관람객은 티켓을 구매해야만 입장할 수 있다.

#### 1.2. 구현

#### 1.2.1. 초대장 클래스

![](./img/example.object.oriented.ticketsales.case1.01.png)

```java
public class Invitation {
    private LocalDateTime when;
}
```

초대장 클래스는 간단하게 구현되었다.
초대일자(when)를 인스턴스 변수로 갖고 있다.

#### 1.2.2. 티켓 클래스

![](./img/example.object.oriented.ticketsales.case1.02.png)

```java
public class Ticket {
    private Long fee;

    public Long getFee() {
        return fee;
    }
}
```

극장에 입장을 하기 위해서는 티켓을 소지해야 한다.
해당 티켓은 추첨을 통해 추첨된 사람들만 초대장을 갖고 있을 것이다.

#### 1.2.3. 가방 클래스

관람객은 소지품을 보관할 Bag을 갖고 있다고 가정한다.

![](./img/example.object.oriented.ticketsales.case1.03.png)

```java
public class Bag {
    private Long amount;
    private Invitation invitation;
    private Ticket ticket;

    public Bag(Long amount) {
        this.amount = amount;
    }

    public Bag(Invitation invitation, Long amount) {
        this.amount = amount;
        this.invitation = invitation;
    }

    public boolean hasInvitation() {
        return invitation != null;
    }

    public boolean hasTicket() {
        return ticket != null;
    }

    public void setTicket(Ticket ticket) {
        this.ticket = ticket;
    }

    public void minusAmount(Long amount) {
        this.amount -= amount;
    }

    public void plusAmount(Long amount) {
        this.amount += amount;
    }
}
```

Bag 클래스는 초대장(invitation), 티켓(ticket), 현금(amount)을 인스턴스 변수로 갖는다.
티켓과 초대장 여부를 판단하는 hasInvitation와 hasTicket 메서드가 있다.
현금을 증가시키거나 감소시키는 plusAmount, minusAmount 메서드도 있다.
여기서 Bag은 현금만 있거나 초대장과 현금이 있는 경우 2가지가 있기에 해당하는 생성자 2개가 있다.

#### 1.2.4. 관람객 클래스

![](./img/example.object.oriented.ticketsales.case1.04.png)

```java
public class Audience {
    private Bag bag;

    public Audience(Bag bag) {
        this.bag = bag;
    }

    public Bag getBag() {
        return bag;
    }
}
```

극장에 입장하려는 관람객은 가방(Bag)을 갖고 있으므로 인스턴스 변수로 포함하고 있다.

#### 1.2.5. 매표소 클래스

![](./img/example.object.oriented.ticketsales.case1.05.png)

```java
public class TicketOffice {
    private Long amount;
    private List<Ticket> tickets = new ArrayList<>();

    public TicketOffice(Long amount, Ticket... tickets) {
        this.amount = amount;
        this.tickets.addAll(Arrays.asList(tickets));
    }

    public Ticket getTicket() {
        return tickets.remove(0);
    }

    public void minusAmount(Long amount) {
        this.amount -= amount;
    }

    public void plusAmount(Long amount) {
        this.amount += amount;
    }
}
```

매표소는 다수의 티켓을 갖고 있다.
매표소에서 티켓을 하나씩 반환하는 getTicket 메서드는 인스턴스 변수인 tickets에서 하나씩 소모한다.
또한 판매 금액을 더하거나 차감하는 plusAmount, minusAmount 메서드가 구현돠어 있다.

#### 1.2.6. 판매원 클래스

![](./img/example.object.oriented.ticketsales.case1.06.png)

```java
public class TicketSeller {
    private TicketOffice ticketOffice;

    public TicketSeller(TicketOffice ticketOffice) {
        this.ticketOffice = ticketOffice;
    }

    public TicketOffice getTicketOffice() {
        return ticketOffice;
    }
}
```

판매원 클래스는 자신이 일하는 매표소(TicketOffice)를 알고 있어야 하므로 인스턴스 변수인 ticketOffice를 갖는다.

#### 1.2.7. 극장 클래스

![](./img/example.object.oriented.ticketsales.case1.07.png)

```java
public class Theater {
    private TicketSeller ticketSeller;

    public Theater(TicketSeller ticketSeller) {
        this.ticketSeller = ticketSeller;
    }

    public void enter(Audience audience) {
        if (audience.getBag().hasInvitation()) {
            Ticket ticket = ticketSeller.getTicketOffice().getTicket();
            audience.getBag().setTicket(ticket);
        } else {
            Ticket ticket = ticketSeller.getTicketOffice().getTicket();
            audience.getBag().minusAmount(ticket.getFee());
            ticketSeller.getTicketOffice().plusAmount(ticket.getFee());
            audience.getBag().setTicket(ticket);
        }
    }
}
```

극장에서 판매 행위를 하는 판매원을 인스턴스 변수인 ticketSeller를 갖는다.
극장 클래스는 enter 메서드를 통해 관람객인 audience를 받아 입장을 처리한다.
여기서 audience의 가방을 호출하여 초대장 소지 여부에 따라 티켓 처리를 하고 있다.

#### 1.2.8. 정리

![](./img/example.object.oriented.ticketsales.case1.08.png)

극장은 관람객을 입장을 처리하기 위해서는 먼저 관람객의 가방을 꺼내 초대장이 들어 있는지 확인한다.
초대장이 있다면 판매원을 통해 매표소를 갖고와서 티켓을 얻고 관람객 가방안에 티켓을 넣는다.
만약 초대장이 없다면 관람객 가방을 갖고와서 금액을 차감하고 위와 동일하게 관람객 가방에 티켓을 넣는다.

#### 1.3. 문제점

#### 1.3.1 수동적인 객체

객체 지향적 관점에서 극장 클래스를 분석해 본다.
극장 클래스는 관람객 입장을 처리하기 위해서 관람객의 가방을 직접 꺼내서 초대장을 확인하고 있다.
게다가 티켓을 얻는 과정에서도 판매원의 매표소를 직접 꺼내서 티켓을 꺼내간다.
그리고 이를 다시 관람객 가방을 꺼내서 초대장을 넣고 있고 초대장이 없는 경우에는 돈을 함부로 건드리고 있다.
현실에서는 관람객이 직접 가방에서 초대장을 꺼내 판매원에게 건넨다.
티켓을 구매하는 관람객은 직접 돈을 가방에서 꺼내 판매원에게 지불한다.
판매원은 매표소의 티켓을 직접 꺼내 관람객에게 주고 돈을 받으며 이를 매표소에 보관한다.
그러나 코드에서는 현실과는 맞지 않는 내용으로 코드가 구현이 되어 있다.

#### 1.3.2 과한 기억 정보

Theater 클래스의 enter 메서드를 보면 세부적인 내용들을 한꺼번에 기억해야 한다.
enter 메서드를 이해하기 위해서는 관람객, 관람객의 가방, 가방안에 초대장 그리고 판매원과 판매원이 근무하는 매표소를 알아야 한다.
관람객이 입장한다는 사실을 이해하기에는 너무 많은 요소들을 알고 기억해야하는 부담감이 있다.

#### 1.3.3 변경에 취약한 코드

![](./img/example.object.oriented.ticketsales.case1.09.png)