# 모듈러 연산 (Modular Arithmetic)

**모듈러 연산**은 정수 연산에서 나머지를 이용하여 계산하는 기법으로, 수학 및 컴퓨터 과학에서 중요한 역할을 합니다. 특히, 큰 수의 계산, 암호학, 알고리즘 문제 해결 등 다양한 분야에서 활용됩니다.

## 개념

- **동치 관계**: 두 정수 aaa와 bbb가 **모듈러 nnn**에 대해 동치라는 것은 nnn으로 나눈 나머지가 같음을 의미합니다. a≡b(modn)iffn ∣ (a−b)a \equiv b \pmod{n} \quad \text{iff} \quad n \, | \, (a - b)a≡b(modn)iffn∣(a−b)
- **나머지 연산자**: 프로그래밍 언어에서 보통 `%` 기호를 사용하며, a%na \% na%n은 aaa를 nnn으로 나눈 나머지를 반환합니다.

## 모듈러 연산의 성질

모듈러 연산은 다음과 같은 연산에 대해 닫혀 있으며, 이러한 성질을 활용하여 복잡한 계산을 단순화할 수 있습니다.

1. **덧셈**:
    $$ (a+b) \; mod \; n = (a \; mod \; n + b \; mod \; n) \; mod \; n$$
2. **뺄셈**:   $$ (a-b) \; mod \; n = (a \; mod \; n - b \; mod \; n+ n) \; mod \; n$$
    - 뺄셈의 경우 음수가 될 수 있으므로 nnn을 더해 양수로 만듭니다.
3. **곱셈**:
    $$ (a * b) \; mod \; n = (a \; mod \; n * b \; mod \; n) \; mod \; n$$
4. **거듭제곱**:
    $$a^b\; mod\; n = ((a\; mod\; n) ^ b mod\; n$$
    - 효율적인 거듭제곱 계산을 위해 **모듈러 지수 법칙**을 사용합니다.

## 모듈러 연산의 응용

- **큰 수의 계산**: 매우 큰 수를 다룰 때 오버플로우를 방지하고 계산을 단순화하기 위해 사용합니다.
- **암호학**: RSA 암호화 등 공개 키 암호에서 큰 수의 모듈러 연산이 핵심입니다.
- **해시 함수**: 데이터의 균등한 분배를 위해 모듈러 연산을 사용합니다.
- **알고리즘 문제 해결**: 제한된 범위 내에서 결과를 표현해야 하는 경우(예: 결과를 109+710^9 + 7109+7로 나눈 나머지를 출력) 모듈러 연산이 사용됩니다.

## 모듈러 연산 구현하기

### 1. 모듈러 덧셈
```Java
public static int modularAddition(int a, int b, int mod) {
    return ((a % mod) + (b % mod)) % mod;
}

```

### 2. 모듈러 뺄셈
```Java
public static int modularSubtraction(int a, int b, int mod) {
    return ((a % mod - b % mod) + mod) % mod;
}

```

### 3.모듈러 곱셉
```Java
public static int modularMultiplication(int a, int b, int mod) {
    return ((a % mod) * (b % mod)) % mod;
}

```

### 4. 모듈러 거듭제곱 (빠른 거듭제곱 알고리즘)
```Java
public static long modularExponentiation(long base, long exponent, long mod) {
    long result = 1;
    base = base % mod;
    while (exponent > 0) {
        if ((exponent & 1) == 1) {
            result = (result * base) % mod;
        }
        exponent = exponent >> 1;
        base = (base * base) % mod;
    }
    return result;
}

```
#### 설명

- **비트 연산자**를 사용하여 지수의 홀짝을 판단합니다.
- 지수를 반으로 줄여가며 거듭제곱을 계산하므로 시간 복잡도는 O(log⁡n)O(\log n)O(logn)입니다.

### 5. 모듈러 역원

모듈러 역원 a−1a^{-1}a−1은 다음 조건을 만족하는 정수입니다
$$ a * a^-1 = 1 \; (mod \; n)$$

### 모듈러 역원 계산 (확장 유클리드 호제법 이용)
```Java
public static long modularInverse(long a, long mod) {
    long[] result = extendedGCD(a, mod);
    long gcd = result[0];
    long x = result[1];
    // 모듈러 역원이 존재하지 않는 경우
    if (gcd != 1) {
        throw new ArithmeticException("Modular inverse does not exist");
    } else {
        // 음수일 경우 양수로 변환
        return (x % mod + mod) % mod;
    }
}

// 확장 유클리드 호제법
private static long[] extendedGCD(long a, long b) {
    if (b == 0) {
        return new long[]{a, 1, 0};
    }
    long[] vals = extendedGCD(b, a % b);
    long gcd = vals[0];
    long x1 = vals[2];
    long y1 = vals[1] - (a / b) * vals[2];
    return new long[]{gcd, x1, y1};
}

```

#### 설명

- **확장 유클리드 호제법**을 사용하여 aaa와 nnn의 최대공약수를 구하고, 모듈러 역원을 계산합니다.
- 모듈러 역원이 존재하려면 aaa와 nnn은 **서로 소**여야 합니다.

## 사용 예시
```Java
public class ModularArithmeticExample {
    public static void main(String[] args) {
        int a = 17;
        int b = 13;
        int mod = 5;

        // 모듈러 덧셈
        int addResult = modularAddition(a, b, mod);
        System.out.println("모듈러 덧셈 결과: " + addResult); // 출력: 0

        // 모듈러 뺄셈
        int subResult = modularSubtraction(a, b, mod);
        System.out.println("모듈러 뺄셈 결과: " + subResult); // 출력: 4

        // 모듈러 곱셈
        int mulResult = modularMultiplication(a, b, mod);
        System.out.println("모듈러 곱셈 결과: " + mulResult); // 출력: 1

        // 모듈러 거듭제곱
        long base = 3;
        long exponent = 13;
        long expResult = modularExponentiation(base, exponent, mod);
        System.out.println("모듈러 거듭제곱 결과: " + expResult); // 출력: 3

        // 모듈러 역원
        try {
            long invResult = modularInverse(a, mod);
            System.out.println("모듈러 역원 결과: " + invResult);
        } catch (ArithmeticException e) {
            System.out.println(e.getMessage());
        }
    }

    // 위에서 구현한 메서드들을 여기에 포함시키면 됩니다.
    // ...
}

```

## 주의사항

- **음수 처리**: 모듈러 연산에서 음수가 발생할 수 있으므로, 결과에 modmodmod를 더하고 다시 modmodmod로 나누어 양수로 만듭니다.
- **나눗셈 연산**: 모듈러 연산에서 직접적인 나눗셈은 불가능하므로, 모듈러 역원을 사용하여 나눗셈을 수행합니다.
- **오버플로우 주의**: 큰 수의 곱셈이나 거듭제곱을 계산할 때 오버플로우가 발생할 수 있으므로, `long` 자료형을 사용하거나 모듈러 연산을 중간 중간 적용하여 오버플로우를 방지합니다.

## 모듈러 연산의 응용 분야 상세

### 1. 암호학

- **RSA 암호화**: 큰 소수의 곱을 이용한 공개 키 암호 알고리즘으로, 모듈러 거듭제곱과 모듈러 역원이 핵심입니다.
- **디지털 서명**: 전자 서명 생성과 검증 과정에서 모듈러 연산이 사용됩니다.

### 2. 알고리즘 문제 해결

- **수열의 합 계산**: 결과가 매우 커질 수 있으므로, 문제에서 특정 수로 나눈 나머지를 요구하는 경우가 많습니다.
- **조합 계산**: 이항 계수를 계산할 때 모듈러 연산을 사용하여 결과를 제한합니다.

### 3. 해시 함수 및 데이터 분배

- **해싱**: 키 값을 테이블 크기로 모듈러 연산하여 인덱스를 결정합니다.
- **로드 밸런싱**: 서버 수로 모듈러 연산하여 요청을 분배합니다.

## 결론

모듈러 연산은 수학적 성질과 효율성을 바탕으로 다양한 분야에서 필수적인 도구로 사용되고 있습니다. 특히, 큰 수의 계산과 제한된 범위 내에서 결과를 유지해야 하는 상황에서 유용합니다. 모듈러 연산의 기본 원리와 구현 방법을 이해하고 응용할 수 있으면 알고리즘 문제 해결과 프로그래밍에서 큰 도움이 됩니다.