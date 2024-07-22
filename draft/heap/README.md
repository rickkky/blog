---
id: heap

title: 堆
---

```rust
struct MaxHeap {
    data: Vec<i32>,
}

impl MaxHeap {
    // Get the left child node index.
    fn left(i: usize) -> usize {
        2 * i + 1
    }

    // Get the right child node index.
    fn right(i: usize) -> usize {
        2 * i + 2
    }

    // Get the parent node index.
    fn parent(i: usize) -> usize {
        (i - 1) / 2
    }

    pub fn new(nums: Vec<i32>) -> Self {
        let mut heap = MaxHeap { data: nums };
        heap.heapify();
        heap
    }

    pub fn heapify(&mut self) {
        for i in (0..=Self::parent(self.size() - 1)).rev() {
            self.sift_down(i);
        }
    }

    pub fn size(&self) -> usize {
        self.data.len()
    }

    pub fn peek(&self) -> Option<i32> {
        self.data.first().copied()
    }

    pub fn push(&mut self, val: i32) {
        self.data.push(val);
        self.sift_up(self.size() - 1);
    }

    fn sift_up(&mut self, mut i: usize) {
        while i > 0 {
            let p = Self::parent(i);
            if self.data[p] >= self.data[i] {
                break;
            }
            self.swap(i, p);
            i = p;
        }
    }

    pub fn pop(&mut self) -> Option<i32> {
        if self.data.is_empty() {
            return None;
        }
        self.swap(0, self.size() - 1);
        let val: Option<i32> = self.data.pop();
        self.sift_down(0);
        val
    }

    fn sift_down(&mut self, mut i: usize) {
        loop {
            let l = Self::left(i);
            let r = Self::right(i);
            let mut j = i;
            if l < self.size() && self.data[l] > self.data[j] {
                j = l;
            }
            if r < self.size() && self.data[r] > self.data[j] {
                j = r;
            }
            if j == i {
                break;
            }
            self.swap(i, j);
            i = j;
        }
    }

    fn swap(&mut self, a: usize, b: usize) {
        self.data.swap(a, b);
    }
}
```

> 参考：
>
> - [Hello 算法 - 堆](https://www.hello-algo.com/chapter_heap/)
