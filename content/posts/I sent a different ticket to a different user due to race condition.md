---
title: 'I once sent a different ticket to a different user due to race condition'
tags: [rca-stories,java, concurrency, thread, race-condition]
date:  "2024-02-03"
---


tldr:
I sent a different ticket to a different user due to race condition

### Problem
We had a consumer which would listen to transaction events and based on that would issue tickets to the corresponding user. Since that was something that could be done concurrently I designed a flow to set up a threadpool and assign the tasks. Problem was that 'data' was part of the object's state and not a parameter so multiple task could see and modify the 'data' resulting in inconsistent behavior.

Following is a simplified version of that issue:

```java
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

class Main{
    public static void main(String[] args) {
       ExecutorService service= Executors.newFixedThreadPool(4);
        Worker worker = new Worker();
       
        String[] works = {"work1","work2","work3"};
        
        for (String eachWork: works){
            service.submit(()->{
                worker.setData(eachWork);
                worker.work();
            });
        }
        service.shutdown();
    }
}


class Worker{
    private String data;
    public void setData(String data){
        this.data=data;
    }

    public void work(){
        System.out.println("Worker did the work with "+data);
    }

}
```





