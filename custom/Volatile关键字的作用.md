### Volatile关键字的作用

使用object字段，线程也可能在本地缓存变量值。
这意味着如果两个线程同时更新同一个对象的变量，并且该变量未被声明为volatile，则可能存在一个线程在缓存中具有旧值的情况



public class VolatileTest extends Thread {
​    
    volatile boolean flag = false;
    int i = 0;
    
    public void run() {
        while (!flag) {
            i++;
        }
    }
    
    public static void main(String[] args) throws Exception {
        VolatileTest vt = new VolatileTest();
        vt.start();
        Thread.sleep(2000);
        vt.flag = true;
        System.out.println("stope" + vt.i);
    }
}