# 티켓 판매 애플리케이션

#### 1. 티켓 판매 애플리케이션 개발

#### 1.1. 요구사항

극장이 있다.
극장에 입장하려는 관람객은 추첨을 통해 초대장을 받은 사람과 받지 못한 사람이 있다.
관람객을 입장시키기 전에 초대장을 갖고 있는지 확인해야 한다.
이를 위해서는 매표소가 있으며 매포소에는 판매원이 있다.
초대장이 있는 관람객은 초대장을 티켓으로 교환 후 입장할 수 있다.
반면에 초대장이 없는 관람객은 티켓을 구매해야만 입장할 수 있다.

#### 1.1.1 초대장 클래스

![](./img/example.object.oriented.ticketsales.case1.01.png)

```java
public class Invitation {
    private LocalDateTime when;
}
```

초대장 클래스는 간단하게 구현되었다.
초대일자(when)를 인스턴스 변수로 갖고 있다.

#### 1.1.2 티켓 클래스

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

#### 1.1.3 가방 클래스

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

#### 1.1.4 관람객 클래스

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

#### 1.1.5 매표소 클래스

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

#### 1.1.6 매표소 클래스

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

판매원 클래스는 자신이 일하는 매표소(TicketOffice)를 알고 있어야 하므로 인스턴스 변수로 ticketOffice를 갖는다.