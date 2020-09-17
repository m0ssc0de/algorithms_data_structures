# Basic of Algorithms and data structures

Go through the [article](https://u.osu.edu/cstutorials/2016/11/21/7-algorithms-and-data-structures-every-programmer-must-know/).

- Sort Algorithms
	1. [Merge Sort](#1.-Merge-Sort)
	2. []

## Sort Algorithms

### 1. Merge Sort

- https://en.wikipedia.org/wiki/Merge_sort

The core of it looks like merge two list into one.

So let's make the funcion, `merge2list` firstly.

At this step, our goal is merging `l1`, `l2` into `l3`.
The len of `l3`, `n`, is sum of `l1` and `l2`.
Every value of `l3` will be set from `l1` or `l2`.

```golang
func main() {
	l1 := []int{1, 2}
	l2 := []int{3, 4}
	merge2list(l1, l2, len(l1) + len(l2))
}
func merge2list(l1, l2 []int, n int) []int{
	l3 := make([]int, n)
	for i := 0; i < n; i++ {
		l3[i] = 0// a placeholder
	}
	return l3
}
```

Then add two var to keep offset. And the offset will not beyound the len of list.
If all offset reach the len of list, there will no item to choose. So `break`.
If one of lists havn't reach the end, set the value of it's offset. Then move the offset.
If none of lists reach the end, choose the small one.

```golang
func merge2list(l1, l2 []int, n int) []int{
	l3 := make([]int, n)
	o1 := 0
	len1 := len(l1)
	o2 := 0
	len2 := len(l2)
	for i := 0; i < n; i++ {
		if (o1 == len1) && (o2 == len2) {
			break
		}
		if o1 == len1 {
			l3[i] = l2[o2]
			o2++
			continue
		}
		if o2 == len2 {
			l3[i] = l1[o1]
			o1++
			continue
		}
		if l1[o1] < l2[o2] {
			l3[i] = l1[o1]
			o1++
			continue
		} else {
			l3[i] = l2[o2]
			o2++
			continue
		}
	}
	return l3
}
```

It looks ok. After test, we know it need some pre-conditions. Every list must be sorted before.
No need to worry. If we start from the list that have only one item, it could be considered as sorted.

Get into the next step. We need find the right two list to merge from the origin list.

Firstly, we must start the first item of origin list. And the iteration will not beyound the end of list.

```golang
func mergeSort(l []int) {
	for i := 0; i < len(l); i++ {

	}
}
```

Then, we know the len of lists, `width`, which will start from 1 to the len of origin list.

```golang
func mergeSort(l []int) {
	n := len(l)
	for width := 1; width < n; width++ {
		for i := 0; i < n; i++ {
		}
	}
}
```

We have known the length of two array and the start of one. Let's try to find the exactily two arrays to pass to `merge2list`. If we start from 0 at all, the list1 will start from `0` and end with `0 + width`. Next list2 is behind it, so list2 start from `0 + width`, end with `0 + width + width`. So we assume it looks like this.


```golang
func mergeSort(l []int) {
	n := len(l)
	for width := 1; width < n; width++ {
		for i := 0; i < n; i++ {
			l1 := l[i : i+width]
			l2 := l[i+width : i+2*width]
		}
	}
}
```

But it's wront when `i = 1`. The list1 shoud start from `2`, not `1`. Essentially the next list1 shoud behind the last list2. So `i++` should be replaced by `i + 2*width`.

```golang
func mergeSort(l []int) {
	n := len(l)
	for width := 1; width < n; width++ {
		for i := 0; i < n; i = i + 2*width {
			l1 := l[i : i+width]
			l2 := l[i+width : i+2*width]
		}
	}
}
```

Then, next question. `width` and `i` will not beyound the `n`. but `i + width` and `i + 2*width` maybe. So we must limite them.

```golang
func min(a, b int) int {
	if a < b {
		return a
	}
	return b
}
func mergeSort(l []int) {
	n := len(l)
	for width := 1; width < n; width++ {
		for i := 0; i < n; i = i + 2*width {
			l1 := l[i : min(i+width, n)]
			l2 := l[min(i+width, n) : min(i+2*width, n)]
		}
	}
}
```

Now we have the input arg of `merge2list`. But how to process the output value of it. In fact the last two list will be merged into one list. This list will be one of lists that next loop will use. So we shoud replace the merged list back.

```golang
func copy(from, to []int, n int){
	for i := 0; i < n; i++ {
		to[i] = from[i]
	}
}

func mergeSort(l []int) {
	n := len(l)
	for width := 1; width < n; width++ {
		for i := 0; i < n; i = i + 2*width {
			l1 := l[i : min(i+width, n)]
			l2 := l[min(i+width, n) : min(i+2*width, n)]
			l3 := merge2list(l1, l2, len(l1) + len(l2))
			copy(l3, l[i : i+len(l3)], len(l3))
		}
	}
}
```

- This implementaion use slice. It could be easy to rewrite by pointer.
- The memory space it will use is double of input list.

> TODO
> test on leetcode
> calculate complexity

```golang
package main

import (
	"fmt"
)

func main() {
	l := []int{8, 4, 9, 6, 7}
	mergeSort(l)
	fmt.Println(fmt.Sprint(l))

}

func min(a, b int) int {
	if a < b {
		return a
	}
	return b
}

func copy(from, to []int, n int){
	for i := 0; i < n; i++ {
		to[i] = from[i]
	}
}

func mergeSort(l []int) {
	n := len(l)
	for width := 1; width < n; width++ {
		for i := 0; i < n; i = i + 2*width {
			l1 := l[i : min(i+width, n)]
			l2 := l[min(i+width, n) : min(i+2*width, n)]
			l3 := merge2list(l1, l2, len(l1) + len(l2))
			copy(l3, l[i:i+len(l3)], len(l3))
		}
	}
}

func merge2list(l1, l2 []int, n int) []int{
	l3 := make([]int, n)
	o1 := 0
	len1 := len(l1)
	o2 := 0
	len2 := len(l2)
	for i := 0; i < n; i++ {
		if (o1 == len1) && (o2 == len2) {
			break
		}
		if o1 == len1 {
			l3[i] = l2[o2]
			o2++
			continue
		}
		if o2 == len2 {
			l3[i] = l1[o1]
			o1++
			continue
		}
		if l1[o1] < l2[o2] {
			l3[i] = l1[o1]
			o1++
			continue
		} else {
			l3[i] = l2[o2]
			o2++
			continue
		}
	}
	return l3
}
```

### 2. Quick Sort

- https://en.wikipedia.org/wiki/Quicksort

The core is partition. Move a item to the correct postiong which have all smller items at it's right, all bigger items at it's left. Then any item at right will not need to change with the left items. So the two group items could be processed isolation.

```golang
func quick(list []int) {
	p := move_and_partition(list)
	leftGroup := list[left:p]
	rightGroup := list[p:right]
	quick(leftGroup)
	quick(rightGroup)
}
func moveAndPartition(list []int) int {
	//
}
```

It's a recursion. So we need declarition the end of it.

```golang
func quick(list []int) {
	if len(list) < 2 {
		return
	}
	p := move_and_partition(list)
	if len(list) = 2 {
		return
	}
	leftGroup := list[left : p]
	rightGroup := list[p+1 : right]
	quick(leftGroup)
	quick(rightGroup)
}
func moveAndPartition(list []int) int {
	//
}
```

So let's get in the details of `moveAndPartition`. Assume we start from the first item, the item `l`. We use this item cmp the last item, then the second one, item `r`. If item `r` is smaller than item `l`, swap them. Then all items at the right of item `r` are bigger than item `r`. The condition, `l != r` will stop the loop when the item `l` is smaller than all item at its right.

```golang
func moveAndPartition(list []int) int {
	l := 0
	r := len(list)
	for l != r {
		if list[r] < list[l] {
			list[r], list[l] = list[l], list[r]
			break
		} else {
			r--
		}
	}
}
```

Then let's change the direction. Increase the `l`. cmp every item `l` with the item `r`. If item `l` is bigger than item `r`. Swap them.

```golang
func moveAndPartition(list []int) int {
	l := 0
	r := len(list)
	for l != r {
		if list[r] < list[l] {
			list[r], list[l] = list[l], list[r]
			break
		} else {
			r--
		}
	}
	for l != r {
		if list[l] > list[r] {
			list[r], list[l] = list[l], list[r]
			break
		} else {
			l++
		}
	}
}
```

Now all item at the left of item `l`. There is a gap between the items which have been compared. Change direction continuely.

```golang
func moveAndPartition(list []int) int {
	l := 0
	r := len(list)
	for l != r {
		for l != r {
			if list[r] < list[l] {
				list[r], list[l] = list[l], list[r]
				break
			} else {
				r--
			}
		}
		for l != r {
			if list[l] > list[r] {
				list[r], list[l] = list[l], list[r]
				break
			} else {
				l++
			}
		}
	}
	return l
}
```

### Bucket Sort

It looks like grouping statistical sample. Then insertion sort at  a specifit group.

So implement insertion sort.

```golang
func insertSort(list []int, value int) []int {
	for i, v:= range list {
		if value > v {
			return insert(list, i, value)
		}
	}
	return append(list, value)
}

func insert(list []int, index, value int) []int {
	list = append(list, 0)
	copy(list[index+1:], list[index:])
	list[index] = value
	return list
}
```