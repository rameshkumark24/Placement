# Day 320A ➕

**L-320A** · ➕ Library management & Amazon-style orders — the problems that look easy

**Time:** 3 hrs · **Mode:** NEW

> **⭐ These two are handed out because they sound like CRUD. Candidates relax, produce an
> entity-relationship diagram, and fail.**
>
> Both hide the same three things: **a physical thing versus its availability, a state machine
> nobody drew, and a limited resource held across a user interaction.** By now you have seen all
> three — **today is about recognising them in a problem that is disguised.**

---

# Part 1 — ⭐ Library management

## ⭐ The split you have now seen four times

```java
class Book { String isbn; String title; boolean isAvailable; }   // ⭐ WRONG — again
```

> **⭐ A `Book` is a title. A `BookItem` is a physical copy with a barcode.** The library owns six
> copies of one ISBN; availability, loans, condition and location belong to the **copy**.

| Problem | ⭐ The thing | ⭐ The availability record |
|---|---|---|
| Cinema (Day 316) | Seat | ⭐ **ShowSeat** |
| Parking (Day 311) | Spot | ⭐ its occupancy over time |
| Library | ⭐ **Book (title)** | ⭐ **BookItem (copy)** |
| Commerce | ⭐ **Product (SKU)** | ⭐ **stock at a location** |

**⭐ Say it as a general rule** — *"the catalogue entity and the unit of availability are always
different entities"* — **because saying it once covers every follow-up.**

## ⭐ The state machine nobody draws

```
   AVAILABLE ──issue──► ON_LOAN ──return──► AVAILABLE
       │  ▲                 │
   reserve│                 │ renew (⭐ blocked if reserved by someone else)
       ▼  │                 ▼
     HELD ─┘            OVERDUE ──► LOST ──► (⭐ replacement charge; terminal)
   ⭐ (for a specific member, with an expiry)
```

| ⭐ Rule | ⭐ Design |
|---|---|
| ⭐ **A hold is a reservation with an expiry** | ⭐ **Day 316's mechanism, unchanged** |
| ⭐ **Reservations queue per title, not per copy** | ⭐ **you want *a* copy, not copy #3** — FIFO across the title |
| ⭐ On return, the head of the queue is notified | ⭐ and gets N days to collect before it passes on |
| ⭐ **Renewal blocked when someone is waiting** | the rule that makes the queue mean anything |
| Overdue → fines | a strategy; **a `Clock` port, always** |
| ⭐ Lost | ⭐ terminal for the copy; the title's count drops |

> **⭐ The per-title queue is the design insight.** A reservation attached to a specific copy is
> wrong: if copy #3 is lost and copy #5 comes back, the member should get #5. **Model the demand
> against the title, satisfy it with whatever copy returns first.**

| Also model | ⭐ Detail |
|---|---|
| ⭐ **Membership limits** | ⭐ max concurrent loans — **checked at issue, and it is a per-member invariant** |
| Loan period by member type | student vs staff — a policy object |
| ⭐ **Fine caps and waivers** | a real rule; unbounded fines are a product bug |
| Search | by title/author/subject — an index, not a scan |

⚠️ **The concurrency case: two librarians issue the same copy at two desks.** Same conditional
update, same `rowsAffected` arbiter as the seat. **And two members claiming one returned copy from
the hold queue** — the queue head is the arbiter, and the claim must be atomic.

---

# Part 2 — ⭐⭐ Amazon-style orders

## ⭐ The pipeline, and where the real question is

```
   Cart ──checkout──► Order ──► Payment ──► Fulfilment ──► Shipment(s) ──► Delivered
                        │                                          ▲
              ⭐ INVENTORY RESERVED HERE                            │
              ⭐ (not at cart, not at fulfilment)          ⭐ possibly SEVERAL
```

> **⭐ When is stock reserved? — is the entire question**, and it is Day 316's seat hold wearing a
> different name.

| ⭐ When | ⭐ Consequence |
|---|---|
| ⭐ **At add-to-cart** | ⭐ abandoned carts hold your entire inventory hostage |
| ⭐ **At checkout, with a TTL** | ⭐⭐ **correct — a reservation released on payment failure or expiry** |
| At payment success | ⭐ you can take money for stock that has just sold out |
| At shipment | ⭐ **oversell by design** — legitimate for some businesses, and it must be a *stated policy* |

**⭐ Say the policy out loud, because oversell is not automatically a bug:** groceries and
made-to-order goods deliberately oversell and reconcile; electronics with one unit left must not.
**"Which of those is this business?" is a genuine clarifying question.**

```sql
-- ⭐ The reservation, exactly Day 316's conditional update
UPDATE stock SET available = available - :qty
 WHERE sku = :sku AND location = :loc AND available >= :qty;   -- ⭐ rowsAffected decides
```

## ⭐ The order state machine — and the arrows that are missing

```
   CREATED → PAYMENT_PENDING → PAID → PICKING → SHIPPED → DELIVERED
       │            │           │        │                    │
       └──► CANCELLED ◄─────────┘        │              ⭐ return → RETURN_REQUESTED
                    ⭐ cancel is only     │                     → RETURNED → REFUNDED
                    ⭐ legal up to here ──┘                ⭐ (a SEPARATE flow — not a reverse arrow)
```

| ⭐ Rule | ⭐ Why |
|---|---|
| ⭐ **Cancellation has a window** | ⭐ once picked, cancelling is a return, not a cancel |
| ⭐ **DELIVERED never goes back** | ⭐ **Day 309's missing arrow — a return is a new flow with its own states** |
| ⭐ **Split shipments are normal** | ⭐ **an order has many shipments; status is derived from them** |
| Partial cancellation | per line item, so the order's status is a rollup |
| ⭐ Payment capture vs authorisation | ⭐ **authorise at checkout, capture at dispatch** — the standard, and it explains the cancellation window |

> **⭐ "The order's status is derived from its shipments' statuses, not stored" is the modelling
> sentence for this problem.** One order, three parcels, two delivered — a single stored status
> cannot express it, and every real e-commerce system hits this.

## ⭐ Idempotency: the double-click

```java
POST /orders   Idempotency-Key: 7f3a...        // ⭐ generated by the CLIENT, per checkout attempt
```

**⭐ The user double-clicks "Place order", the mobile network retries, the payment callback arrives
twice.** All three produce duplicate orders and duplicate charges without an idempotency key (Day 122)
— **and this is the single most common real bug in the domain.** Store the key with the result;
a repeat returns the original order rather than creating a second.

---

# Part 3 — ⭐⭐ The unifying lesson

**⭐ Do this exercise properly — it is the reason this day exists.**

| Problem | The limited resource | ⭐ The mechanism |
|---|---|---|
| Parking (311) | a spot | ⭐ CAS on occupancy |
| Cinema (316) | a seat | ⭐ **hold with expiry + conditional update** |
| ATM (317) | account balance | ⭐ conditional decrement |
| Cab (320) | a driver | ⭐ **offer with timeout + CAS** |
| Library (320A) | a copy | ⭐ **hold with expiry + a queue** |
| Commerce (320A) | stock | ⭐ **reservation with TTL + conditional decrement** |

> **⭐ Six problems. One mechanism. A limited resource, claimed atomically, held with an expiry
> because a human is in the loop, and released by a read-time predicate rather than a sweeper.**

**⭐ Saying that in an interview is worth more than any individual solution**, because it shows you
have generalised rather than memorised. **It is also true**, which is why it works on problems you
have not seen.

**⭐ And the second unifying rule, from Days 290, 315 and 320:** anything a user is charged for or
judged by must be **reproducible from the inputs frozen at the time** — the rate, the multiplier, the
ruleset, the price. **Two rules, eleven problems.**

---

## ⭐ Traps

| Trap | Consequence |
|---|---|
| ⭐ `isAvailable` on `Book` | ⭐ six copies, one flag |
| ⭐ **Reservations against a specific copy** | ⭐ a member waits for a copy that was lost |
| Renewal allowed while others wait | the hold queue means nothing |
| No hold expiry | a notified member never collects; the copy is frozen |
| Unbounded fines | a product bug, and a support queue |
| ⭐ Reserving stock at add-to-cart | ⭐ abandoned carts hold the inventory |
| ⭐ Reserving after payment | ⭐ **money taken for stock that is gone** |
| ⭐ **A single stored order status** | ⭐⭐ **split shipments cannot be represented** |
| A reverse arrow for returns | the refund and audit trail disappear |
| ⭐ No idempotency key on checkout | ⭐ **double-click, double order, double charge** |
| Capturing payment at checkout | cancellation becomes a refund unnecessarily |
| ⭐ Treating either problem as CRUD | ⭐ you miss all three hidden problems |

## Interview questions

**Q: Why is `Book` not the thing you lend?**
Because a library has six copies of one title. Availability, condition, location and loan history all
belong to the physical copy, so `Book` is the catalogue entry and `BookItem` is the unit of
availability. It is the same split as seat versus show-seat and product versus stock — the catalogue
entity and the unit of availability are always different entities.

**Q: How do reservations work in a library?**
A queue per title, not per copy — you want a copy, not copy number three — and a hold with an expiry
once one becomes available. When a copy is returned, the head of the queue is notified and has a few
days to collect before it passes on. Renewals are blocked while anyone is waiting, otherwise the
queue is decorative.

**Q: When do you reserve inventory in an e-commerce order?**
At checkout, with a TTL, released on payment failure or expiry. At add-to-cart, abandoned carts hold
your stock hostage; after payment, you can take money for something that just sold out. It is the
seat-hold mechanism again. Whether overselling is acceptable is a business question worth asking —
groceries reconcile, single-unit electronics must not.

**Q: An order ships in three parcels, two arrive. What is its status?**
Derived, not stored. The order's status is a rollup over its shipments, because a single field cannot
express "two of three delivered". The same applies to partial cancellations, which happen per line
item. That derivation is the modelling decision the problem is actually testing.

**Q: The user double-clicks "Place order".**
Without an idempotency key you get two orders and two charges — and the same happens on a mobile
network retry or a duplicated payment callback. The client sends a key per checkout attempt, the
server stores it with the result, and a repeat returns the original order. It is the most common real
bug in this domain.

**Q: What do these problems have in common with the ones before them?**
They are all a limited resource claimed atomically and held across a human. Spot, seat, balance,
driver, copy, stock — the mechanism is the same every time: a conditional update or compare-and-set
decides the winner, the hold carries an owner and an expiry, and expiry is evaluated at read time
rather than by a sweeper.

## Mini task

1. Model `Book` and `BookItem`. **Write the test that fails with `isAvailable` on `Book`**: six
   copies, one loan, five still available.
2. Implement the **per-title hold queue** with notification on return and a collection expiry.
   **Test: the notified member does not collect, and the hold passes to the next.**
3. Block renewal while a hold exists. **Write the test.**
4. Implement fines with an injected `Clock` and a cap. Advance time rather than sleeping.
5. ⭐ Two librarians issuing the same copy: **conditional update, 100 threads, assert one loan.**
6. ⭐ Implement checkout-time **stock reservation with a TTL.** Test: reserve, fail the payment,
   confirm the stock returns **without a sweeper running.**
7. ⭐ Build the **order status as a derived rollup** over shipments. Deliver two of three parcels and
   assert `PARTIALLY_DELIVERED`. **Then try to do it with a stored field and see what breaks.**
8. ⭐ Add an **idempotency key** to checkout. Fire the same request 50 times concurrently and **assert
   exactly one order.**
9. ⭐ **Write the unifying table yourself, from memory** — six problems, the resource, the mechanism.

# 🚪 Exit questions

1. State the catalogue-versus-availability rule and give four instances of it.
2. Why do library reservations queue per title?
3. What makes a hold queue meaningful, and what makes it decorative?
4. Give four possible reservation points for stock and the consequence of each.
5. When is overselling a legitimate policy?
6. Why must an order's status be derived?
7. Why is a return not a reverse arrow?
8. Explain authorise-versus-capture and how it relates to the cancellation window.
9. Give three ways a duplicate order is created and the one fix.
10. State the unifying mechanism across six problems in one sentence.
11. State the second unifying rule and name the three problems it came from.

## 🎙️ Articulation drill

**Three minutes:** *"Design an order management system."*

**⭐ The marker: within the first minute you ask when inventory is reserved, and you frame it as a
hold with an expiry** — because that reframes a CRUD-sounding problem into the one you have already
solved five times.

**⭐ The stronger marker: you say so.** *"This is the same shape as a cinema seat — a limited resource
held across a user interaction — so I'll use a reservation with a TTL and a conditional update."*
**Generalising across problems is the single most senior thing you can do in an LLD round**, and it
is what the last eleven days were for.

---

**Previous:** [Day 320](Day-320.md) · **Next:** [Day 321](Day-321.md) — 🚪🚪 the Stage 8 exit gate
