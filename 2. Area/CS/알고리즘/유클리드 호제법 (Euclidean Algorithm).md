**유클리드 호제법**은 두 정수의 최대공약수(GCD, Greatest Common Divisor)를 효율적으로 계산하는 알고리즘입니다. 이 알고리즘은 기원전 300년경에 유클리드가 제시한 것으로 알려져 있으며, 수학 및 컴퓨터 과학 분야에서 널리 사용되고 있습니다.

## 개념

- **최대공약수(GCD)**: 두 정수의 공통된 약수 중 가장 큰 수를 의미합니다.
- **호제법**: "나눗셈을 반복한다"는 의미로, 두 수의 나머지를 이용하여 최대공약수를 구하는 방법입니다.

## 알고리즘의 원리

유클리드 호제법은 다음과 같은 원리로 동작합니다.

1. 두 정수 aaa와 bbb (a>ba > ba>b)가 주어졌을 때, aaa를 bbb로 나눕니다.
2. 나머지 rrr이 0이 아니면, bbb를 rrr로 나누는 과정을 반복합니다.
3. 나머지가 0이 될 때의 나누는 수가 최대공약수입니다.

### 수식으로 표현
![[Pasted image 20241124145422.png]]

## 자바로 구현하기

### 재귀적 구현
```Java
public class EuclideanAlgorithm {
    // 재귀적으로 최대공약수 계산
    public static int gcd(int a, int b) {
        if (b == 0) {
            return a;
        } else {
            return gcd(b, a % b);
        }
    }

    // 테스트를 위한 main 메서드
    public static void main(String[] args) {
        int a = 48;
        int b = 18;
        int result = gcd(a, b);

        System.out.println(a + "와 " + b + "의 최대공약수는 " + result + "입니다.");
    }
}

```

### 반복적 구현
```Java
public class EuclideanAlgorithm {
    // 반복적으로 최대공약수 계산
    public static int gcd(int a, int b) {
        while (b != 0) {
            int r = a % b;
            a = b;
            b = r;
        }
        return a;
    }

    // 테스트를 위한 main 메서드
    public static void main(String[] args) {
        int a = 1071;
        int b = 462;
        int result = gcd(a, b);

        System.out.println(a + "와 " + b + "의 최대공약수는 " + result + "입니다.");
    }
}

```

## 확장 유클리드 호제법

확장 유클리드 호제법은 **베주 항등식(Bézout's identity)**에 따라 두 정수 aaa와 bbb에 대해 다음 식을 만족하는 정수 xxx와 yyy를 찾는 알고리즘입니다.

$$ ax+by=gcd(a,b)$$
### 구현
```Java
public class ExtendedEuclideanAlgorithm {
    // 확장 유클리드 호제법
    public static int extendedGCD(int a, int b, int[] xy) {
        if (b == 0) {
            xy[0] = 1;
            xy[1] = 0;
            return a;
        }
        int gcd = extendedGCD(b, a % b, xy);
        int x = xy[1];
        int y = xy[0] - (a / b) * xy[1];
        xy[0] = x;
        xy[1] = y;
        return gcd;
    }

    // 테스트를 위한 main 메서드
    public static void main(String[] args) {
        int a = 240;
        int b = 46;
        int[] xy = new int[2];
        int gcd = extendedGCD(a, b, xy);

        System.out.println(a + "와 " + b + "의 최대공약수는 " + gcd + "입니다.");
        System.out.println("계수 x와 y는 각각 " + xy[0] + ", " + xy[1] + "입니다.");
        System.out.println(a + "*" + xy[0] + " + " + b + "*" + xy[1] + " = " + gcd);
    }
}

```

## 시간 복잡도 분석

- **시간 복잡도**: O(log min(a, b))
    - 유클리드 호제법은 각 단계에서 나머지가 급격히 감소하므로 로그 시간 복잡도를 가집니다.
- **공간 복잡도**:
    - 재귀적 구현: 재귀 호출로 인해 호출 스택에 O(log min(a, b))의 공간이 필요합니다.
    - 반복적 구현: 상수 공간을 사용합니다.

## 유클리드 호제법의 응용 분야

- **분수의 기약화**: 분수의 분자와 분모를 최대공약수로 나누어 기약 분수로 만듭니다.
    
- **RSA 암호화 알고리즘**: 큰 소수의 최대공약수를 구하는 데 사용됩니다.
    
- **모듈러 연산**: 모듈러 역원 계산 등에서 확장 유클리드 호제법을 사용합니다.
    
- **공약수와 공배수 계산**: 최소공배수(LCM)를 구할 때 최대공약수를 활용합니다.
    
    $$LCM(a,b) = a×b​​​ \over gcd(a,b) $$
    

## 유클리드 호제법의 역사

- **기원**: 고대 그리스의 수학자 유클리드가 제시한 알고리즘으로, 그의 저서 '원론(Elements)'에 수록되어 있습니다.
- **의의**: 가장 오래된 알고리즘 중 하나로, 현대까지도 그 효율성과 단순함으로 널리 사용되고 있습니다.

## 결론

유클리드 호제법은 두 수의 최대공약수를 효율적으로 계산하는 기본적인 알고리즘입니다. 그 단순한 원리와 효율성으로 인해 수학과 컴퓨터 과학 분야에서 광범위하게 활용되고 있습니다. 재귀적, 반복적 구현 모두 이해하기 쉽고 실제 프로그래밍에서도 자주 사용되므로, 알고리즘의 원리를 잘 이해하고 구현할 수 있는 것이 중요합니다.