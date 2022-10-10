---
title: "코틀린: for문"
categories:
- 코틀린
---

# for문

- [docs: For loops](https://kotlinlang.org/docs/control-flow.html#for-loops\)

```kotlin
for (item in collection) {
    print(item)
}
```

기본적으로 요래 구현

in 으로

```kotlin
for (i in 1..3) {
    println(i)
}
```

범위 할 때 점 2개로 땡땡

```kotlin
for (i in 6 downTo 0 step 2) {
    println(i)
}
```

6

4

2

0

요렇게 찍힘

6 downTo 0 step 2

말 그대로 6에서 0으로 2칸 씩

```kotlin
for (i in array.indices) {
    println(array[i])
}
```

배열에 있는 목록 돌리려면 이렇다는데

indices 안써도 되지 않나

array[i]로 찍는거 보니까

indices 쓰면 index로 돌리나봄

```kotlin
for ((index, value) in array.withIndex()) {
    println("the element at $index is $value")
}
```

요거는 index, value 같이 돌리기
