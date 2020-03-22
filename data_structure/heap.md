## 堆(heap)
堆是一种用数组实现的二叉树的数据结构。由于堆必须是完全二叉树，因此查找节点的左右子节点时可以通过数组的下标实现。
堆总是有以下性质：
* 堆是一棵完全二叉树
* 堆中某个节点的值总是大于等于（满足这种性质的堆称为“大根堆”）或者小于等于（满足这种性质的堆称为“小根堆”）其子节点的值。
* 堆中每个节点的子树都是堆树

堆的常用方法：
* 构建优先队列（如Java中的PriorityQueue）
* 支持堆排序
* 快速找出集合中的最小值或者最大值

### 堆的操作
#### 1、堆的构建
堆的构建可以认为是不断加入元素的过程，或者是一个数组不断向下调整的过程（jdk中使用向下调整的方式实现了输入参数为Collection的堆初始化）。

#### 2、元素插入
在数组末尾添加一个元素，然后将其与父元素相比，循环往上。

#### 3、元素取出
获取数组的第一个元素输出，将剩下的元素进行重新排列。

#### 代码实现
这是我参考Java源码写出来的一个简单版的堆，其中包含了基本的操作，以及可以定制的比较器。
```
	public class Heap<E> {

	Object[] queue;
	
	private int size;
	
	private static final int DEFAULT_SIZE = 16;
	
	private Comparator<? extends E> comparator;
	
	public Heap() {
		this(DEFAULT_SIZE, null);
	}
	
	public Heap(Comparator<? extends E> comparator) {
		this(DEFAULT_SIZE, comparator);
	}
	
	public Heap(int initialCapacity, Comparator<? extends E> comparator) {
		this.queue = new Object[initialCapacity];
	}
	
	public void grow(int size) {
//		当容量不足时，增加25%
		int oldCapacity = queue.length;
		int newCapacity = oldCapacity + (oldCapacity<=64?oldCapacity + 2:oldCapacity>>2);
		queue = Arrays.copyOf(queue, newCapacity);
	}
	
//	取出元素
	public E poll() {
		if (size == 0)
            return null;
		int s = --size;
        E result = (E) queue[0];
        E x = (E) queue[s];
        queue[s] = null;
        if (s != 0)
        	shiftDown(0, x);
        return result;
	}
	
//	插入元素
	public boolean add(E e) {
		if(e == null)
			throw new NullPointerException();
		int i = size;
		if(i >= queue.length) {
			grow(i + 1);
		}
		shiftUp(i, e);
		size ++;
		return true;
	}
	
	private void shiftDown(int k, E x) {
		Comparable<? super E> key = (Comparable<? super E>)x;
        int half = size >>> 1;
        while (k < half) {
            int child = (k << 1) + 1; 
            Object c = queue[child];
            int right = child + 1;
            if (right < size &&
                ((Comparable<? super E>) c).compareTo((E) queue[right]) > 0)
                c = queue[child = right];
            if (key.compareTo((E) c) <= 0)
                break;
            queue[k] = c;
            k = child;
        }
        queue[k] = key;
	}
	
	private void shiftUp(int k, E e) {
		Comparable<? super E> key = (Comparable<? super E>) e;
		while(k > 0) {
			int parent = (k -1) >>> 1; 
			Object t = queue[parent];
			if(key.compareTo((E) t) >= 0) {
				break;
			}
			queue[k] = t;
			k = parent;
		}
		queue[k] = key;
	}
}
```



#### 参考文献
1、[数据结构：堆（Heap)](https://www.jianshu.com/p/6b526aa481b1)
2、[百度百科](https://baike.baidu.com/item/%E5%A0%86/20606834?fr=aladdin)
3、[数据结构——堆（Heap）大根堆、小根堆](https://www.cnblogs.com/wangchaowei/p/8288216.html)