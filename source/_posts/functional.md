---
title: 方法传参-function参数
date: 2020-02-06 15:00:51
tags: java基础
---
## 场景一：转述第三方方法
如果有个第三方sdk的某个类里面有各种详情的方法，但是使用的时候为了更好的观看性，希望使用本项目的各种命名规则， 同时也希望使用lambda表示，这时候可以开发一个桥接类。
``` bash
第三方类client：
public class Client {
    public Integer add(Integer i, Integer j) {
        System.out.println("i+j=" + (i + j));
        return i + j;
    }
}
只用来做lambda的接口：
@FunctionalInterface
public interface ESCallBack<T> {

    public T doSearch(Client client);
}

桥接类，其中search1，search2就是给当前项目用的 ：
public class NativeMethodClient {

    private Client client;

    public NativeMethodClient(Client client) {
        this.client = client;
    }

    private <T> T execute(ESCallBack<T> action) {

        Preconditions.checkNotNull(action, "Callback object must not be null");
        return action.doSearch(client);
    }

    public Integer search1() {
        return execute((c) -> c.add(10, 20));
    }

      public Integer search2() {
        return execute((c) -> c.add(10, 20));
    }
}
```

## 场景二：模板模式-回调方法自定义
``` bash
public class GeneratelService<T> {
    public <T> void generate(Integer i, Supplier<T> handler) {
        System.out.println(i);
        handler.get();
    }
}

调用方：
    public void serviceTest() {
        functionalService.doSomething(6, () -> {
            System.out.println("自己定义方法");
            return null;
        });

    }
```

