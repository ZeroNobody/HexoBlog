---
title: List使用add方法添加数据时的覆盖问题
date: 2018-12-25 10:38:55
tags:
---

有时候我们用对象封装数据后添加到list链表中会发现，最后添加的那个对象覆盖了前面所有的数据，虽然数据的总数（list.size()）和实际情况一样，但是数据却不是我们想要的

例如下面：

	 	ResultSet rs = stmt.executeQuery("select * from " + tableName);
        List<ImsData> dataList = new ArrayList<ImsData>();
        ImsData imsData = new ImsData();
        while(rs.next()){          
            imsData.setBenchmarkCmName(rs.getString(1));
            imsData.setMeasureCmName(rs.getString(2));
            dataList.add(imsData);
        }

很明显，代码的目的是将数据库中查寻的数据封装到dataList中并返回，可实际上，这段代码将返回查询数据的最后一条数据，并且数据量的总数和数据库（select count(*) from tableName）数据量一样,这是为什么呢，因为在循环开始前，我们新建了一个imsData对象，在后面封装数据的时候，我们每次都使用的是同一个对象，所以每次都是对这个对象的操作，这就导致了每添加一个数据的时候，前一个对象的数据也就被重新设置了，所以就发生了所谓的对象数据覆盖。


要解决这一问题也很简单，那就是每次循环的时候新建一个对象，这样每次添加进入list集合的都是一个新对象，也就不会覆盖原对象的值了

	  	ResultSet rs = stmt.executeQuery("select * from " + tableName);
        List<ImsData> imsDataList = new ArrayList<ImsData>();
        while(rs.next()){
            ImsData imsData = new ImsData();
            imsData.setBenchmarkCmName(rs.getString(1));
            imsData.setMeasureCmName(rs.getString(2));
            imsDataList.add(imsData);
        }


问题解决