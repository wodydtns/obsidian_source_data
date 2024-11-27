**조합론**은 이산수학의 한 분야로, 유한하거나 가산적인 집합의 구성 요소들을 세거나 배열하는 방법을 연구합니다. 이는 수학적 구조의 조합, 배열, 선택에 대한 이론을 다루며, 확률론, 통계학, 컴퓨터 과학 등 다양한 분야에서 응용됩니다.

## 기본 개념

1. **순열(Permutation)**: 특정한 순서로 배열된 객체들의 집합입니다.
    - 예: 서로 다른 n개의 원소 중 r개를 선택하여 순서를 고려하여 배열하는 방법의 수.
2. **조합(Combination)**: 순서를 고려하지 않고 객체들을 선택하는 방법입니다.
    - 예: 서로 다른 n개의 원소 중 r개를 선택하는 방법의 수.
3. **중복 조합(Combination with Repetition)**: 동일한 원소를 여러 번 선택할 수 있는 조합.
4. **분할(Partition)**: 집합을 부분집합들로 나누는 방법.

## 기본 공식

### 1. 순열의 수

- **n개의 원소를 모두 배열하는 경우**:
    
    $$P(n)=n!$$
- **n개의 원소 중 r개를 선택하여 배열하는 경우**:
    
    $$P(n, r) = \frac{n!}{(n - r)!}$$​

### 2. 조합의 수

- **n개의 원소 중 r개를 선택하는 경우**
$$C(n, r) = \binom{n}{r} = \frac{n!}{r!(n - r)!}$$​

### 3. 중복 조합의 수

- **n종류의 물건 중에서 r개를 선택하는 중복 조합의 수**:
$$C(n + r - 1, r) = \binom{n + r - 1}{r}$$

## 주요 원리

### 1. 덧셈 원리 (Addition Principle)

- 서로 배타적인 사건 A와 B가 있을 때, 사건 A 또는 B가 발생하는 경우의 수는 각각의 경우의 수의 합입니다. 
$$총 경우의 수=경우의 수(A)+경우의 수(B)$$

### 2. 곱셈 원리 (Multiplication Principle)

- 연속된 독립적인 사건 A와 B가 있을 때, 사건 A와 B가 모두 발생하는 경우의 수는 각각의 경우의 수의 곱입니다. 
$$총 \;경우의 \,수=경우의\, 수(A)+경우의\, 수(B)$$

### 3. 포함-배제 원리 (Inclusion-Exclusion Principle)

- 두 집합 A와 B의 합집합의 원소의 개수는 각 집합의 원소의 개수의 합에서 교집합의 원소의 개수를 뺀 것입니다.
$$∣A∪B∣=∣A∣+∣B∣−∣A∩B∣$$

### 4. 비둘기집 원리 (Pigeonhole Principle)

- n개의 비둘기를 m개의 비둘기집에 넣을 때, n>m 이면 적어도 하나의 비둘기집에는 두 개 이상의 비둘기가 있습니다.

## 조합론의 응용 분야

- **확률론**: 사건의 확률 계산.
- **통계학**: 표본 추출과 데이터 분석.
- **암호학**: 키의 생성과 암호 해독 방법.
- **컴퓨터 과학**: 알고리즘의 복잡도 분석, 데이터 구조 설계.
- **네트워크**: 경로의 수 계산, 연결성 분석.

## 예제 문제와 풀이

### 예제 1: 순열 계산

**문제**: 5명의 학생 중에서 3명을 뽑아 줄을 세우는 방법의 수는 몇 가지인가요?

**풀이**:

- **선택 단계**: 5명 중 3명을 선택 (순서 고려).
- **계산**
$$P(5,3)=5!(5−3)!=5!2!=1202=60P(5, 3) = \frac{5!}{(5 - 3)!} = \frac{5!}{2!} $$
$$ p(5, 3) = \frac{5!}{(5 - 3)!} = \frac{5!}{2!} = \frac{120}{2} = 60 $$
**답**: 60가지.

### 예제 2: 조합 계산

**문제**: 10개의 서로 다른 책 중에서 4권을 선택하는 방법의 수는 몇 가지인가요?

**풀이**:

- **선택 단계**: 순서를 고려하지 않고 4권 선택.
- **계산**
$$ C(10,4) = (\frac{10}{4}) = \frac{10!}{4!6!} = 210$$
**답**: 210가지.

### 예제 3: 중복 조합

**문제**: 3종류의 과일(사과, 바나나, 오렌지)이 무한히 있을 때, 5개의 과일을 선택하는 방법의 수는 몇 가지인가요?

**풀이**:

- **중복 조합**: n = 3, r = 5.
- **계산**
$$C(n+r−1,r)=(3+5−15)=(75)=21C(n + r - 1, r) = \binom{3 + 5 - 1}{5} = \binom{7}{5} = 21C(n+r−1,r)=(53+5−1​)=(57​)=21$$

$$ C(n+r−1,r)=(\frac{3+1-1}{5})= \frac{7}{5} = 21$$
**답**: 21가지.

### 예제 4: 포함-배제 원리

**문제**: 1부터 100까지의 자연수 중에서 2의 배수 또는 3의 배수인 수의 개수는 몇 개인가요?

**풀이**:

- **전체 개수**: 100개.
- **2의 배수 개수**: ⌊1002⌋=50\left\lfloor \frac{100}{2} \right\rfloor = 50⌊2100​⌋=50개.
- **3의 배수 개수**: ⌊1003⌋=33\left\lfloor \frac{100}{3} \right\rfloor = 33⌊3100​⌋=33개.
- **공배수(6의 배수) 개수**: ⌊1006⌋=16\left\lfloor \frac{100}{6} \right\rfloor = 16⌊6100​⌋=16개.
- **포함-배제 원리 적용**: 50+33−16=6750 + 33 - 16 = 6750+33−16=67

**답**: 67개.

## 파스칼의 삼각형과 이항 계수

**파스칼의 삼각형**은 이항 계수를 삼각형 모양으로 배열한 것으로, 조합론에서 중요한 역할을 합니다.

### 생성 방법

- 첫 번째 행과 첫 번째 열은 1로 채웁니다.
- 각 원소는 바로 위의 두 원소의 합입니다.

### 파스칼의 삼각형 예시

markdown

코드 복사

        `1       1   1     1   2   1   1   3   3   1 1   4   6   4   1`

- 각 행의 숫자는 이항 계수와 동일합니다. n번째 행의 k번째 원소=(nk)\text{n번째 행의 k번째 원소} = \binom{n}{k}n번째 행의 k번째 원소=(kn​)

## 이항 정리 (Binomial Theorem)

이항 정리는 다음과 같이 전개됩니다:

(a+b)n=∑k=0n(nk)an−kbk(a + b)^n = \sum_{k=0}^{n} \binom{n}{k} a^{n - k} b^{k}(a+b)n=k=0∑n​(kn​)an−kbk

**예시**:

(a+b)3=(30)a3+(31)a2b+(32)ab2+(33)b3=a3+3a2b+3ab2+b3(a + b)^3 = \binom{3}{0} a^3 + \binom{3}{1} a^2 b + \binom{3}{2} a b^2 + \binom{3}{3} b^3 = a^3 + 3a^2 b + 3a b^2 + b^3(a+b)3=(03​)a3+(13​)a2b+(23​)ab2+(33​)b3=a3+3a2b+3ab2+b3


## 조합론의 컴퓨터 구현

조합론 계산을 컴퓨터로 구현할 때는 큰 수의 팩토리얼 계산과 이로 인한 오버플로우를 주의해야 합니다. 이를 해결하기 위해 동적 프로그래밍이나 모듈러 연산을 사용합니다.

### 예제: 이항 계수 계산 (동적 프로그래밍 사용)

```Java
public class CombinationCalculator {
    // 이항 계수 계산 (동적 프로그래밍)
    public static long combination(int n, int k) {
        long[][] C = new long[n + 1][k + 1];

        for (int i = 0; i <= n; i++) {
            int limit = Math.min(i, k);
            for (int j = 0; j <= limit; j++) {
                if (j == 0 || j == i) {
                    C[i][j] = 1; // nC0 = nCn = 1
                } else {
                    C[i][j] = C[i - 1][j - 1] + C[i - 1][j];
                }
            }
        }
        return C[n][k];
    }

    // 테스트를 위한 main 메서드
    public static void main(String[] args) {
        int n = 10;
        int k = 4;
        long result = combination(n, k);
        System.out.println("C(" + n + ", " + k + ") = " + result);
    }
}

```

### 모듈러 연산을 이용한 이항 계수 계산
큰 수의 계산에서 결과를 특정 수로 나눈 나머지를 구해야 할 때 모듈러 연산을 사용합니다.

```Java
public class CombinationModulo {
    static final int MOD = 1000000007;

    // 팩토리얼 계산 및 모듈러 연산
    public static long factorialMod(int n) {
        long result = 1;
        for (int i = 2; i <= n; i++) {
            result = (result * i) % MOD;
        }
        return result;
    }

    // 모듈러 역원 계산 (페르마의 소정리 이용)
    public static long modInverse(long a) {
        return modularExponentiation(a, MOD - 2);
    }

    // 모듈러 거듭제곱
    public static long modularExponentiation(long base, int exponent) {
        long result = 1;
        base %= MOD;
        while (exponent > 0) {
            if ((exponent & 1) == 1) {
                result = (result * base) % MOD;
            }
            base = (base * base) % MOD;
            exponent >>= 1;
        }
        return result;
    }

    // 이항 계수 계산
    public static long combinationMod(int n, int k) {
        if (k == 0 || n == k) {
            return 1;
        }
        long numerator = factorialMod(n);
        long denominator = (factorialMod(k) * factorialMod(n - k)) % MOD;
        return (numerator * modInverse(denominator)) % MOD;
    }

    // 테스트를 위한 main 메서드
    public static void main(String[] args) {
        int n = 1000;
        int k = 500;
        long result = combinationMod(n, k);
        System.out.println("C(" + n + ", " + k + ") mod " + MOD + " = " + result);
    }
}

```

# 순열, 조합, 중복 조합 계산기
```Java
import java.math.BigInteger;

public class CombinatoricsCalculator {

    // n!을 계산하는 메서드
    public static BigInteger factorial(int n) {
        BigInteger result = BigInteger.ONE;
        for (int i = 2; i <= n; i++) {
            result = result.multiply(BigInteger.valueOf(i));
        }
        return result;
    }

    // 순열 계산: P(n, r) = n! / (n - r)!
    public static BigInteger permutation(int n, int r) {
        if (r > n || n < 0 || r < 0) {
            return BigInteger.ZERO;
        }
        BigInteger numerator = factorial(n);
        BigInteger denominator = factorial(n - r);
        return numerator.divide(denominator);
    }

    // 조합 계산: C(n, r) = n! / (r! * (n - r)!)
    public static BigInteger combination(int n, int r) {
        if (r > n || n < 0 || r < 0) {
            return BigInteger.ZERO;
        }
        // 조합 계산 최적화: 작은 수의 팩토리얼만 계산
        BigInteger result = BigInteger.ONE;
        for (int i = 0; i < r; i++) {
            result = result.multiply(BigInteger.valueOf(n - i))
                           .divide(BigInteger.valueOf(i + 1));
        }
        return result;
    }

    // 중복 조합 계산: C(n + r - 1, r) = (n + r - 1)! / (r! * (n - 1)!)
    public static BigInteger combinationWithRepetition(int n, int r) {
        if (n <= 0 || r < 0) {
            return BigInteger.ZERO;
        }
        return combination(n + r - 1, r);
    }

    // 테스트를 위한 main 메서드
    public static void main(String[] args) {
        int n = 10;
        int r = 3;

        BigInteger perm = permutation(n, r);
        BigInteger comb = combination(n, r);
        BigInteger combRep = combinationWithRepetition(n, r);

        System.out.println("순열 P(" + n + ", " + r + ") = " + perm);
        System.out.println("조합 C(" + n + ", " + r + ") = " + comb);
        System.out.println("중복 조합 C(" + (n + r - 1) + ", " + r + ") = " + combRep);
    }
}

```

## 코드 설명

- **`factorial` 메서드**: `BigInteger`를 사용하여 n!을 계산합니다.
    - 2부터 n까지의 수를 곱하여 팩토리얼을 계산합니다.
- **`permutation` 메서드**: 순열의 수를 계산합니다.
    - 공식:
    $$ P(n,r)= \frac{n!}{(n - r)!}$$​
    - `n`과 `r`의 값이 유효한지 확인합니다.
- **`combination` 메서드**: 조합의 수를 계산합니다.
    - 공식:
    $$ C(n,r) = \frac{n!}{r!(n - r)!} $$​
    - 계산 최적화를 위해 팩토리얼 전체를 계산하지 않고 곱셈과 나눗셈을 반복합니다.
- **`combinationWithRepetition` 메서드**: 중복 조합의 수를 계산합니다.
    - 공식
    $$C(n+r−1,r)$$
    - `combination` 메서드를 활용하여 계산합니다.
- **`main` 메서드**: 각 함수의 예시를 실행하고 결과를 출력합니다.