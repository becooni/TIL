# Strategy Pattern

> https://ko.wikipedia.org/wiki/%EC%A0%84%EB%9E%B5_%ED%8C%A8%ED%84%B4

```kotlin
import java.util.*
import kotlin.Comparator

interface PaymentStrategy {
    fun pay(amount: Int)
}

class CreditCardStrategy(
    private val name: String,
    private val cardNumber: String,
    private val cvv: String,
    private val dateOfExpire: String
) : PaymentStrategy {

    override fun pay(amount: Int) {
        // CreditCard pay logic
    }
}

class NaverPayStrategy(
    private val id: String,
    private val password: String
) : PaymentStrategy {

    override fun pay(amount: Int) {
        // NaverPay pay logic
    }
}

class SkPayStrategy(
    private val id: String,
    private val password: String
) : PaymentStrategy {

    override fun pay(amount: Int) {
        // SkPay pay logic
    }
}

class KakaoPayStrategy(
    private val id: String,
    private val password: String
) : PaymentStrategy {

    override fun pay(amount: Int) {
        // KakaoPay pay logic
    }
}

fun example() {
    val amount = 10000

    val paymentMethod = "선택된 결제방법 종류"

    val paymentStrategy =
        when (paymentMethod){
            "CreditCard" -> CreditCardStrategy("jake", "123123", "888", "03/24")
            "NaverPay" -> NaverPayStrategy("naver", "password")
            "SkPay" -> SkPayStrategy("tid", "password")
            "KakaoPay" -> KakaoPayStrategy("kakaoid", "password")
            else -> throw IllegalArgumentException("Unsupported $paymentMethod payment method.")
        }

    paymentStrategy.pay(amount)
}
```