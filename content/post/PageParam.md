+++
date = "2017-05-08T10:34:38+08:00"
Categories = ["development"
]
Tags = []
title = "SpringMVC中的简单分页实现"
menu = "main"
+++

* 分页每各项目都在使用，却不是很清楚，以至于每次写都要翻看以前的资料。
* 这次在学习SpringMVC的时候又用到了，就在这整理一遍。我的demo只实现了一个首页。

* 首先建立需要用的实体类Article,然后是PageParam,分别实现当前页、总页、总记录数、页面大小、和需要分页的数据（比如我这用List<Article>）的set、get方法。
* 代码如下:

<code>

    private int currPage ; // 当前页
    
    private int totalPage ; // 总页数
    
    private int rowCount ; // 总记录数
    
    public static int pageSize = 6; // 页大小
    
    private List<Article> data ; // 数据

<code/>

* 需要注意的是setRowCount方法需要进行如下处理：

<code>

    public void setRowCount(int rowCount) {
        int totalPage = rowCount / pageSize; //总页数/页大小
        if (rowCount % pageSize > 0) {
            totalPage += 1;  //如果取余大于0，则总页数加一
        }
        setTotalPage(totalPage);
        this.rowCount = rowCount;
        }

<code/>

* 接下来在IndexController中设置当前页为1，处理从页面传来的页面参数转为int型，

<code>

    @Controller
    public class IndexController {
    
    @Resource
    ArticleService aService;
    
    @RequestMapping(value = "index")
    public String index(HttpServletRequest request){
        String currPageStr = request.getParameter("page");
        int currPage =1;
        try {
            currPage = Integer.parseInt(currPageStr);
        } catch (NumberFormatException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        }
        int rowCount = aService.getRowCount();
        PageParam pageParam = new PageParam();
        pageParam.setRowCount(rowCount);
        if(pageParam.getTotalPage()<currPage){
            currPage = pageParam.getTotalPage();
        }
        pageParam.setCurrPage(currPage);
        pageParam = aService.getListByPage(pageParam);
        request.setAttribute("pageParam", pageParam);
        return "index";
        }
    }
</code>

* 在Service中设置处理方法

<code>
   
    @Service
    public class ArticleService {
    
    @Resource
    ArticleDao articleDao;
    public int getRowCount() {
        // TODO Auto-generated method stub
        return articleDao.getRowCount();
    }
    public PageParam getListByPage(PageParam pageParam) {
        // TODO Auto-generated method stub
        int currPage = pageParam.getCurrPage();
        int offset = (currPage -1) * PageParam.pageSize;
        int size = pageParam.pageSize;
        Map<String, Object> param = new HashMap<String,Object>();
        param.put("offset", offset);
        param.put("size", size);
        List<Article> articles = articleDao.selectByParams(param);
        pageParam.setData(articles);
        return pageParam;
        }
    }

</code>

* 在ArticleDao中设置接口

<code>
    
    @Repository
    public interface ArticleDao {
    int getRowCount();
    List<Article> selectByParams(Map<String, Object> param);
    }

</code>

* 在ArticleMapper中设置sql语句

<code>

    <?xml version="1.0" encoding="UTF-8"?>
    <!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
    <mapper namespace = "com.zzb.myblog.dao.ArticleDao">
    <select id="getRowCount" resultType="int">
        select count(*) from article
    </select>
    <select id="selectByParams" resultType = "com.zzb.myblog.entity.Article" parameterType="map">
        select * from article
        order by id asc
        limit ${offset}, ${size}
    </select>
    </mapper>

</code>

* jsp页面的设置

<code>
    
    <li><a href="#"><%
            PageParam pageParam = (PageParam)request.getAttribute("pageParam");
            int currPage = pageParam.getCurrPage();
            int totalPage = pageParam.getTotalPage();
            for(int i = 1; i <= totalPage; i ++){
                if(i == currPage){
                %><span class="current"><%=currPage %></span><%
                }else{
                %><a href="index.html?page=<%=i %>"><%=i %></a><%
                }
                }
             %></a></li>

</code>

## 整个demo在我的GitHub：

* https://github.com/pugongyingbo/myblog-ssm