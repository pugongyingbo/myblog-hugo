+++
date = "2017-08-06T14:43:10+08:00"
title = "map的几种遍历"
Tags = []
Categories = ["development"]

+++
* 近期用到了map遍历，也查找了部分资料，自己整理一下
* 使用场景是我要从数据库获取一个人的几种奖励类别，但是我要把每个人的类别拼接起来，在service里使用map遍历拼接在一起，在回传到页面。
    * 1、通过Map.entrySet遍历key和value
    * 2、通过Map.entrySet使用iterator遍历key和value
    * 3、通过Map.keySet遍历key和value
    * 4、通过Map.values()遍历所有的value，但不能遍历key


```
    import java.util.HashMap;
    import java.util.Iterator;
    import java.util.Map;
    /**
     * Created by zzb on 2017/8/6.
     */
    public class MapDemo {
    public static void main(String[] args){
        Map<String,Integer> map = new HashMap<String,Integer>();
        map.put("a",1);
        map.put("b",2);
        map.put("c",3);
        //最常用的遍历方式，键值对都需要时使用
        for (Map.Entry<String,Integer> entry:map.entrySet()) {
            System.out.println("Key = "+entry.getKey()+",value = "+entry.getValue());
        }
        //只获取key或者value
        for (String key:map.keySet()) {
            System.out.println("key = " +key);
        }
        for (Integer value:map.values()) {
            System.out.println("value = " +value);
        }
        //使用Iterator遍历
        Iterator<Map.Entry<String, Integer>> entries = map.entrySet().iterator();
        while (entries.hasNext()){
            Map.Entry<String,Integer> entry = entries.next();
            System.out.println("key= "+entry.getKey() +",value = "+entry.getValue());
        }
        //通过键找值遍历 效率低
        for (String key:map.keySet()) {
            Integer value = map.get(key);
            System.out.println("key = "+ key +",value = " +value);
        }
    }
    }
```

* 项目中实际用到的map


```
    public List<JfhjlQueryBean>  selectJfhjlList(JfhjlQueryBean cx) {
    List<JfhjlQueryBean> jfjlBeans =  jlDao.selectJfhjlList(cx);
     //剔除重复信息
       Map<String, JfhjlQueryBean> zfjfs = new HashMap<String,          
     JfhjlQueryBean>();
    //统计各个奖励详细情况
    Map<String, HashMap<String, Integer>> jfjlinfo  = new HashMap<String, HashMap<String, Integer>>();
    //遍历数据
    for (JfhjlQueryBean jfhjlQueryBean : jfjlBeans) {
    String zfbh = jfhjlQueryBean.getZfbh();
    if (zfjfs.get(zfbh) == null) {
        zfjfs.put(zfbh, jfhjlQueryBean);
    }   
    String jllb = jfhjlQueryBean.getJllb();
    //如果包含此人，则奖励类别的value加1  
    if(!jfjlinfo.containsKey(zfbh)){
        HashMap<String, Integer> jfjl = new HashMap<String, Integer>();
        jfjl.put(jllb, 1);
        jfjlinfo.put(zfbh, jfjl);
    }              
    else {
    HashMap<String,Integer> hashMap = jfjlinfo.get(zfbh);
    Integer jllbnum = hashMap.get(jllb);
    if (jllbnum != null) {
        hashMap.put(jllb, (jllbnum+1));
    } else {
       hashMap.put(jllb, 1);
    }
    }
    //结果处理
    List<JfhjlQueryBean> resultAll = new ArrayList<JfhjlQueryBean>();
        for(Map.Entry<String, JfhjlQueryBean> entry:zfjfs.entrySet()){
            StringBuffer jlinfo=new StringBuffer();
            Map<String, Integer> jldetail=jfjlinfo.get(entry.getKey());
            if(!jldetail.equals(null)){
                for(Map.Entry<String, Integer> entry2:jldetail.entrySet()){
                    jlinfo.append(entry2.getValue()+"个"+getJlmc(entry2.getKey())+"\n");
                }
                entry.getValue().setWsyjl(jlinfo.toString());
                resultAll.add(entry.getValue());
            }
            }
        return resultAll;
    }
```
