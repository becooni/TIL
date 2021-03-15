# 코딩테스트

출처: 비공개

테스트 플랫폼 : testdome

총 6문제 시간은 1시간 40분 (문제별 시간 부여)

- 안드로이드 객관식 3문제
- 자바 알고리즘 3문제

기억나는대로 떠올려 보자

## 객관식 1

RecyclerView 관련 문제

- life cycle - onCreateViewHolder는 매번 호출되는지?
-> X
- adapter rebinding 시 addItem 호출되는지?
-> X
- notifyDataSetChanged() 대신 notifyInsert...()를 쓰는게 더 좋은지?
-> O
- addItem 되면 리스트가 animated 되는지?
-> O
- onCreateViewHolder의 인자 viewType은 getItemViewType()로 얻어오는건지?
-> O

## 객관식 2

Fragment Transaction 관련 문제

```java
fragmentManager.beginTransaction();
fragmentManager.add(layoutId, fragment1);
fragmentManager.commit();

fragmentManager.beginTransaction();
fragmentManager.replace(layoutId, fragment2);
fragmentManager.addToBackStack(null);
fragmentManager.commit();
```

- 백버튼 누르면 액티비티 종료 -> X
- fragment1 은 렌더링 되어있다 -> X (fragment2 replace 이기때문에)
- fragment2 는 렌더링 되어있다 -> O
- 백버튼 2번 누르면 액티비티 종료 -> O
- 두번쨰 commit()할떄 크래시 -> X

## 객관식 3

Manifest 관련문제

- Intent filter main 관련 (메인액티비티)
- 테마
- application 태그에 label 속성이 string resource로 되어있으면 translatable 한지?

## 알고리즘 1

정수로 구성된 배열에서 요소 2개의 합이 가장 큰것을 구하는 문제

풀이는 맞았지만 퍼포먼스 에러...

정렬후 최대값과, 두번째 최대값을 더하기 때문에 시간복잡도는 최선 O(N) 최악은 O(N^2)

```java
import java.util.Comparator;
import java.util.List;
import java.util.Arrays;

public class MaxSum {
    public static int findMaxSum(List<Integer> list) {
        list.sort(Comparator.naturalOrder());

        int answer = list.get(list.size() - 2) + list.get(list.size() - 1);

        return answer;
    }

    public static void main(String[] args) {
        List<Integer> list = Arrays.asList(5, 9, 7, 11);
        System.out.println(findMaxSum(list));
    }
}
```

### 개선 !!!

for문 한번에 max1, max2 모두 구함 시간복잡도 O(N)

```java
public static int findMaxSum(List<Integer> list) {

        System.out.println("sort: " + list);

        int max1 = 0;
        int max2 = 0;

        for (int i = 0; i < list.size(); i++) {
            if (max2 < list.get(i)) {
                if (max1 < max2) {
                    max1 = list.get(i);
                } else {
                    max2 = list.get(i);
                }
            }
        }

        return max1 + max2;
    }
```

## 알고리즘 2

유닉스 퍼미션 converter 문제

- r : 4
- w : 2
- x : 1
- \- : 0

ex) rwxrw--w- -> 752

이건 만점~!

```java
public class ReadWriteExecute {

    private static final int[] SCORES = {4, 2, 1};
    private static final String[] CHARS = {"r", "w", "x"};

    public static int symbolicToOctal(String permString) {

        String[] c1 = permString.substring(0, 3).split("");
        String[] c2 = permString.substring(3, 6).split("");
        String[] c3 = permString.substring(6, 9).split("");

        System.out.println(c1.toString());

        String[][] perms = {c1, c2, c3};

        int[] scores = new int[3];

        for (int i = 0; i < 3; i++) {
            for (int j = 0; j < 3; j++) {
                if (perms[i][j].equals(CHARS[j])) {
                    scores[j] += SCORES[j];
                }
            }
        }

        String temp = String.format("%d%d%d", scores[0], scores[1], scores[2]);

        int answer = Integer.parseInt(temp);
        return answer;
    }

    public static void main(String[] args) {
        // Should write 752
        System.out.println(ReadWriteExecute.symbolicToOctal("rwxr-x-w-"));
    }
}
```

## 알고리즘 3

렌터카 예약 유효성 체크 문제

RentalTime에 예약 시간 (렌트 시작, 종료)를 입력하고 겹치는 시간이 없는지 체크하는 문제

풀이는 맞았지만 퍼포먼스 에러

```java
import java.util.*;
import java.text.SimpleDateFormat;
import java.util.stream.Collectors;

public class CarRental {
    public static Boolean canScheduleAll(Collection<RentalTime> rentalTimes) {
        ArrayList<RentalTime> rent = rentalTimes.stream().collect(Collectors.toCollection(ArrayList::new));

        rent.sort((o1, o2) -> {
            if(o1.getStart().after(o2.getStart())) {
                return -1;
            } else {
                return 1;
            }
        });

        for (int i = 0; i < rent.size() - 1; i++) {
            for (int j = i + 1; j < rent.size(); j++) {
                System.out.print(rent.get(i).getEnd() + " - ");
                System.out.println(rent.get(j).getStart());

                if (rent.get(i).getEnd().after(rent.get(j).getStart())) {
                    return false;
                }
            }
        }

        return true;
    }

    public static void main(String[] args) throws Exception {
        SimpleDateFormat sdf = new SimpleDateFormat("d/M/y H:m");

        ArrayList<RentalTime> rentalTimes = new ArrayList<RentalTime>();
        rentalTimes.add(new RentalTime(sdf.parse("03/05/2020 19:00"), sdf.parse("03/05/2020 20:30")));
        rentalTimes.add(new RentalTime(sdf.parse("03/05/2020 22:10"), sdf.parse("03/05/2020 22:30")));
        rentalTimes.add(new RentalTime(sdf.parse("03/05/2020 20:30"), sdf.parse("03/05/2020 22:00")));

        System.out.println(CarRental.canScheduleAll(rentalTimes));
    }
}

class RentalTime {
    private Date start, end;

    public RentalTime(Date start, Date end) {
        this.start = start;
        this.end = end;
    }

    public Date getStart() {
        return this.start;
    }

    public Date getEnd() {
        return this.end;
    }
}
```

### 개선 !!! 

start date 기준으로 오름차순으로 정렬하고 한번의 포문으로 완전탐색

```java
public static Boolean canScheduleAll(Collection<RentalTime> rentalTimes) {
        ArrayList<RentalTime> rentalList = new ArrayList<>(rentalTimes);

        rentalList.sort(Comparator.comparing(RentalTime::getStart));

        RentalTime temp = rentalList.get(0);

        for (int i = 1; i < rentalList.size(); i++) {

            RentalTime rentalTime = rentalList.get(i);

            if (temp.getEnd().compareTo(rentalTime.getStart()) > -1) {
                return false;
            } else {
                temp = rentalTime;
            }
        }

        return true;
    }
```