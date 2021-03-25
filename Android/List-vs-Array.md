# List vs Array

출처

https://stackoverflow.com/questions/36262305/difference-between-list-and-array-types-in-kotlin

https://medium.com/asos-techblog/what-you-didnt-know-about-arrays-in-kotlin-d3b20337e4

- data class의 `Array` property를 쓰면 컴파일 경고가 뜸
- equals(), hashCode(), toString() 등 data class가 자동으로 생성해주는 메소드가 없기 때문
- 되도록 list를 쓰도록하자