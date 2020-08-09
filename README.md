# Producer-Consumer-Problem
Producer-Consumer-Problem

Producer-Consumer is a kind of design pattern where <br />
a. Both producer and consumer can work at different spreed. <br />
b. One doesn't need to know about the existance of other. <br />
c. Separating producer and consumer results in a cleaner and more redable code. <br />
d. Easy to manage both.

## Psudeo code of producer and consumer problem with sempahore

Sempahore full = 0; <br />
Sempahore empty = n; <br />
Sempahore mutex = 1; <br />

Producer()
==============
```
void Producer() {
	int itemp;

	while(true) {
	produce(itemp);

	wait(empty);
	wait(mutex);

	Buffer[in] = temp;
	in = (in + 1) % n;

	signal(mutex);
	signal(full);
	}
}
```

Consumer()
====================
```
void Consumer() {
	int itempc;
	
	while(true) {
		
	wait(full);
	wait(mutex);
	
	itempc = Buffer[out];
	out = (out + 1) % n;
	
	signal(mutex);
	signal(empty);
	
	consume(itempc);
	}
}
```
## Java code 

```
package com.demo;

class Sempahore {
    int s;

    public Sempahore(int s) {
        this.s = s;
    }

    public synchronized void waitS() {
        s--;
        if (s < 0)
            try {
                wait();
            } catch (InterruptedException e) {
                System.out.println("Exception in waitS");
                e.printStackTrace();
            }
    }

    public synchronized void singalS() {
        s++;
        if (s <= 0)
            notify();
    }
}

class BlockingQueue<T> {
    T[] array;
    int size;
    int in;
    int out;

    Sempahore mutex;
    Sempahore full;
    Sempahore empty;

    @SuppressWarnings("unchecked")
    public BlockingQueue(int size) {
        this.size = size;
        array = (T[]) new Object[size];
        mutex = new Sempahore(1);
        full = new Sempahore(0);
        empty = new Sempahore(size);
        in = 0;
        out = 0;
    }

    public void add(T item) {
        empty.waitS();
        mutex.waitS();

        array[in] = item;
        in = (in + 1) % size;

        full.singalS();
        mutex.singalS();
    }

    public T poll() {
        T item;

        full.waitS();
        mutex.waitS();

        item = array[out];
        out = (out + 1) % size;

        empty.singalS();
        mutex.singalS();
        return item;
    }
}

public class ProducerConsumerDemo {

    public static void main(String[] args) throws InterruptedException {
        BlockingQueue<Integer> q = new BlockingQueue<>(5);

        Thread t1 = new Thread(new Runnable() {
            @Override
            public void run() {
                for (int i = 0; i < 20; i++) {
                    q.add(i);
                    System.out.println("Inserted: " + i);
                }
            }
        });

        Thread t2 = new Thread(new Runnable() {
            @Override
            public void run() {
                for (int i = 0; i < 10; i++) {
                    System.out.println("Removed from thread 2: " + q.poll());
                }
            }
        });

        Thread t3 = new Thread(new Runnable() {
            @Override
            public void run() {
                for (int i = 0; i < 10; i++) {
                    System.out.println("Removed from thread 3: " + q.poll());
                }
            }
        });

        t1.start();
        Thread.sleep(4000);
        t2.start();
        t2.join();
        t3.start();
        t1.join();
        t3.join();

        System.out.println("Terminated succesfully!");
    }
}
```


