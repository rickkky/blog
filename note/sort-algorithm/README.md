---
id: sort-algorithm

title: 排序算法
---

# 评价维度

- 运行效率：我们期望排序算法的时间复杂度尽量低，且总体操作数量较少（时间复杂度中的常数项变小）。
- 就地性：原地排序通过在原数组上直接操作实现排序，无须借助额外的辅助数组，从而节省内存。通常情况下，原地排序的数据搬运操作较少，运行速度也更快。
- 稳定性：稳定排序在完成排序后，相等元素在数组中的相对顺序不发生改变。在多级排序场景下，稳定排序是必要的。
- 自适应性：自适应排序的时间复杂度会受输入数据的影响，即最佳时间复杂度、最差时间复杂度、平均时间复杂度并不完全相等。自适应性需要根据具体情况来评估。如果最差时间复杂度差于平均时间复杂度，说明排序算法在某些数据下性能可能劣化，因此被视为负面属性；而如果最佳时间复杂度优于平均时间复杂度，则被视为正面属性。
- 通用性：基于比较的排序算法依赖比较运算符来判断元素之间的相对顺序，理论上的最优时间复杂度为 $O(n \log{n})$ ；而非比较排序不使用比较运算符，时间复杂度可以达到 $O(n)$ ，但其通用性相对较差。

# 选择排序

选择排序（Selection Sort）的工作原理是：循环遍历元素，每轮从未排序区间选择最小的元素，将其放到已排序区间的末尾。

```rust
fn selection_sort(nums: &mut [i32]) {
    if nums.is_empty() {
        return;
    }
    let n = nums.len();
    for i in 0..n - 1 {
        // Store the index of the minimum element.
        let mut k = i;
        for j in i + 1..n {
            if nums[j] < nums[k] {
                k = j;
            }
        }
        nums.swap(i, k);
    }
}
```

- 时间复杂度为 $O(n^2)$ ；非自适应排序。
- 空间复杂度为 $O(1)$ 。
- 原地排序。
- 非稳定排序。选择排序在进行元素交换时，可能会改变相等元素的相对顺序。

# 冒泡排序

冒泡排序（Bubble Sort）的工作原理是：循环遍历元素，每轮从头开始比较相邻元素，如果逆序则交换。这个过程就像气泡从底部升到顶部一样，因此得名冒泡排序。

```rust
fn bubble_sort(nums: &mut [i32]) {
    if nums.is_empty() {
        return;
    }
    let n = nums.len();
    for i in (1..n).rev() {
        let mut swapped = false;
        for j in 0..i {
            if nums[j] > nums[j + 1] {
                nums.swap(j, j + 1);
                swapped = true;
            }
        }
        // The array is sorted if no elements are swapped in this round.
        if !swapped {
            break;
        }
    }
}
```

- 时间复杂度为 $O(n^2)$ ；自适应排序。如果数组本身是正序的，则最优时间复杂度为 $O(n)$ 。
- 空间复杂度为 $O(1)$ 。
- 原地排序。
- 稳定排序。

# 插入排序

插入排序（Insertion Sort）的工作原理是：循环遍历元素，每轮将当前元素插入到已排序区间的合适位置。这个过程类似于手动整理一副扑克牌的过程。

```rust
fn insertion_sort(nums: &mut [i32]) {
    if nums.is_empty() {
        return;
    }
    let n = nums.len();
    for i in 1..n {
        let val = nums[i];
        for j in (0..=i).rev() {
            if j == 0 || nums[j - 1] <= val {
                nums[j] = val;
                break;
            }
            nums[j] = nums[j - 1];
        }
    }
}
```

- 时间复杂度为 $O(n^2)$ ；自适应排序。如果数组本身是正序的，则最优时间复杂度为 $O(n)$ 。
- 空间复杂度为 $O(1)$ 。
- 原地排序。
- 稳定排序。

## 插入排序的优势

相较于快速排序，插入排序的时间复杂度更高，但在数据量较小的情况下，插入排序通常更快。这是因为快速排序这类基于分治策略的排序算法，往往包含更多单元计算操作。在数据量较小时， $n^2$ 与 $n \log{n}$ 较为接近，算法的时间复杂度不是影响运行效率的主导因素，每轮循环中的单元操作数量更为重要。

并且，相较于选择排序和冒泡排序，插入排序的优势在于：

- 选择排序是非自适应排序，任何情况下的时间复杂度都是 $O(n^2)$ 。并且选择排序不稳定，无法应用于多级排序。
- 复杂度的常数系数小。冒泡排序基于元素交换实现，需要借助一个临时变量，共涉及 3 个单元操作；插入排序基于元素赋值实现，仅需 1 个单元操作。因此，冒泡排序的计算开销通常比插入排序更高。

在很多编程语言中的内置排序算法中，当数据量较小时，通常会使用插入排序来提高性能。

# 快速排序

快速排序（Quick Sort）是一种基于分治策略的排序算法，运行高效，应用广泛。

快速排序的核心操作是“哨兵划分”，其目标是：选择数组中的某个元素作为“基准数”，将所有小于基准数的元素移到其左侧，而大于基准数的元素移到其右侧。

```rust
fn partition(nums: &mut [i32], left: usize, right: usize) -> usize {
    let base = nums[left];
    let mut i = left;
    let mut j = right;
    while i < j {
        // Move j first.
        while i < j && nums[j] >= base {
            j -= 1;
        }
        while i < j && nums[i] <= base {
            i += 1;
        }
        nums.swap(i, j);
    }
    nums.swap(i, left);
    i
}

fn quick_sort_part(nums: &mut [i32], left: usize, right: usize) {
    if left >= right {
        return;
    }
    let pivot = partition(nums, left, right);
    if pivot > left {
        quick_sort_part(nums, left, pivot - 1);
    }
    if pivot < right {
        quick_sort_part(nums, pivot + 1, right);
    }
}

fn quick_sort(nums: &mut [i32]) {
    quick_sort_part(nums, 0, nums.len() - 1);
}
```

- 时间复杂度为 $O(n \log{n})$ ；自适应排序。在最差情况下，每轮哨兵划分操作都将长度为 $n$ 的数组划分为长度为 $0$ 和 $n - 1$ 的两个子数组，此时最差时间复杂度为 $O(n^2)$ 。
- 空间复杂度为 $O(n)$ 。在最差情况下，每轮哨兵划分操作都将长度为 $n$ 的数组划分为长度为 $0$ 和 $n - 1$ 的两个子数组，这意味着每一层递归调用减少的问题规模非常小（只减少一个元素），递归的深度为 $n$ 。
- 原地排序。
- 非稳定排序。

## 快速排序的优势

相较于归并排序和堆排序，快速排序的优势在于：

- 出现最差情况的概率很低。虽然快速排序的最差时间复杂度为 $O(n^2)$ ，没有归并排序稳定，但在绝大多数情况下，快速排序能在 $O(n \log{n})$ 的时间复杂度下运行。
- 缓存使用效率高。在执行哨兵划分操作时，系统可将整个子数组加载到缓存，因此访问元素的效率较高。而像堆排序这类算法需要跳跃式访问元素，因此缺乏这一特性。
- 复杂度的常数系数小。相较于归并排序和堆排序，快速排序的比较、赋值、交换等操作的总数量最少。

## 基准数优化

快速排序在某些输入下的时间效率可能降低。例如，如果数组本身是有序的，由于我们选择最左端元素作为基准数，这种情况下分治策略将失效，时间复杂度将退化为 $O(n^2)$ 。

为了尽量避免这种情况发生，我们可以优化哨兵划分中的基准数的选取策略。例如，我们可以随机选取一个元素作为基准数。然而，如果运气不佳，每次都选到不理想的基准数，效率仍然不尽如人意。需要注意的是，编程语言通常生成的是伪随机数。如果我们针对伪随机数序列构建一个特定的测试用例，那么快速排序的效率仍然可能劣化。

为了进一步改进，我们可以在数组中选取三个候选元素（通常为数组的首、尾、中点元素），并将这三个候选元素的中位数作为基准数。这样一来，基准数“既不太小也不太大”的概率将大幅提升。当然，我们还可以选取更多候选元素，以进一步提高算法的稳健性。

```rust
fn median_three(nums: &[i32], left: usize, right: usize) -> usize {
    let mid = left + (right - left) / 2;
    let l = nums[left];
    let m = nums[mid];
    let r = nums[right];
    if (l <= m && m <= r) || (r <= m && m <= l) {
        mid
    } else if (m <= l && l <= r) || (r <= l && l <= m) {
        left
    } else {
        right
    }
}

fn partition(nums: &mut [i32], left: usize, right: usize) -> usize {
    nums.swap(median_three(nums, left, right), left);
    let base = nums[left];
    let mut i = left;
    let mut j = right;
    while i < j {
        // Move j first.
        while i < j && nums[j] >= base {
            j -= 1;
        }
        while i < j && nums[i] <= base {
            i += 1;
        }
        nums.swap(i, j);
    }
    nums.swap(i, left);
    i
}

fn quick_sort_part(nums: &mut [i32], left: usize, right: usize) {
    if left >= right {
        return;
    }
    let pivot = partition(nums, left, right);
    if pivot > left {
        quick_sort_part(nums, left, pivot - 1);
    }
    if pivot < right {
        quick_sort_part(nums, pivot + 1, right);
    }
}

fn quick_sort(nums: &mut [i32]) {
    quick_sort_part(nums, 0, nums.len() - 1);
}
```

## 尾递归优化

为了防止栈帧空间的累积，我们可以在每轮哨兵排序完成后，比较两个子数组的长度，仅对较短的子数组进行递归。这种方法能确保递归深度不超过 $\log{n}$，将最差空间复杂度优化至 $O(\log{n})$ 。

```rust
fn median_three(nums: &[i32], left: usize, right: usize) -> usize {
    let mid = left + (right - left) / 2;
    let l = nums[left];
    let m = nums[mid];
    let r = nums[right];
    if (l <= m && m <= r) || (r <= m && m <= l) {
        mid
    } else if (m <= l && l <= r) || (r <= l && l <= m) {
        left
    } else {
        right
    }
}

fn partition(nums: &mut [i32], left: usize, right: usize) -> usize {
    nums.swap(median_three(nums, left, right), left);
    let base = nums[left];
    let mut i = left;
    let mut j = right;
    while i < j {
        // Move j first.
        while i < j && nums[j] >= base {
            j -= 1;
        }
        while i < j && nums[i] <= base {
            i += 1;
        }
        nums.swap(i, j);
    }
    nums.swap(i, left);
    i
}

fn quick_sort_part(nums: &mut [i32], left: usize, right: usize) {
    let mut left = left;
    let mut right = right;
    while left < right {
        let pivot = partition(nums, left, right);
        // Recurrsively sort the smaller part.
        if pivot - left < right - pivot {
            if pivot > left {
                quick_sort_part(nums, left, pivot - 1);
            }
            left = pivot + 1;
        } else {
            if pivot < right {
                quick_sort_part(nums, pivot + 1, right);
            }
            right = pivot - 1;
        }
    }
}

fn quick_sort(nums: &mut [i32]) {
    quick_sort_part(nums, 0, nums.len() - 1);
}
```

# 归并排序

归并排序（Merge Sort）是一种基于分治策略的排序算法，主要分为两个阶段：

- 划分阶段：通过递归不断地将数组从中点处分开，将长数组的排序问题转换为短数组的排序问题。
- 合并阶段：当子数组长度为 1 时终止划分，开始合并，持续地将左右两个较短的有序数组合并为一个较长的有序数组，直至结束。

```rust
fn merge(nums: &mut [i32], left: usize, mid: usize, right: usize) {
    let mut temp = vec![0; right - left + 1];
    let mut i = left;
    let mut j = mid + 1;
    let mut k = 0;
    while i <= mid && j <= right {
        if nums[i] <= nums[j] {
            temp[k] = nums[i];
            i += 1;
        } else {
            temp[k] = nums[j];
            j += 1;
        }
        k += 1;
    }
    while i <= mid {
        temp[k] = nums[i];
        i += 1;
        k += 1;
    }
    while j <= right {
        temp[k] = nums[j];
        j += 1;
        k += 1;
    }
    for i in 0..temp.len() {
        nums[left + i] = temp[i];
    }
}

fn merge_sort_part(nums: &mut [i32], left: usize, right: usize) {
    if left >= right {
        return;
    }
    let mid = left + (right - left) / 2;
    merge_sort_part(nums, left, mid);
    merge_sort_part(nums, mid + 1, right);
    merge(nums, left, mid, right);
}

fn merge_sort(nums: &mut [i32]) {
    merge_sort_part(nums, 0, nums.len() - 1);
}
```

- 时间复杂度为 $O(n \log{n})$ ；非自适应排序。
- 空间复杂度为 $O(n)$ 。
- 非原地排序。
- 稳定排序。

## 链表排序

将归并排序应用在链表排序问题中，可以将空间复杂度优化至 $O(1)$ 。

- 划分阶段：可以使用迭代的方式替代递归，省去递归使用的栈帧空间。
- 合并阶段：由于链表的增删操作仅需改变引用（指针）即可，因此将两个有序链表合并为一个有序链表的过程无需创建额外的链表。

> 参考：
>
> - [Hello 算法 - 排序](https://www.hello-algo.com/chapter_sorting/)
