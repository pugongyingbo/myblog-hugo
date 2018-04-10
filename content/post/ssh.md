+++
Categories = ["development"]
Tags = []
date = "2017-05-15T19:13:29+08:00"
title = "SSH三大框架配置及整合"
menu = "main"
+++
* 学了三大框架有一段时间，虽然现在用的很少了，但是面试还是会问到，再次整理一下
* 这个是做一个模仿电力公司的项目，包括前后台
* 首先是spring配置，在applicationContext.xml中

<code>

    <!-- 启用组件扫描 -->
    <context:component-scan base-package="com.py" />
    <!-- 连接数据源-->
    <bean id="dataSource" class="org.apache.commons.dbcp.BasicDataSource">
        <property name="driverClassName" value="com.mysql.jdbc.Driver">
        </property>
        <property name="url" value="jdbc:mysql://localhost:3306/sshtest">
        </property>
        <property name="username" value="root"></property>
        <property name="password" value="root"></property>
    </bean>
    <!-- 会话工厂 整合hibernate-->
    <bean id="sessionFactory"
        class="org.springframework.orm.hibernate3.LocalSessionFactoryBean">
        <property name="dataSource">
            <ref bean="dataSource"></ref>
        </property>
         <!-- 注入Hibernate相关的属性 -->
        <property name="hibernateProperties">
            <props>
            <!--<prop key="hibernate.cache.provider_class">org.hibernate.cache.EhCacheProvider</prop>
            <prop key="hibernate.cache.use_second_level_cache">true</prop>
            <prop key="hibernate.cache.use_query_cache">true</prop>
            -->
            <prop key="hibernate.current_session_context_class">thread</prop>
            
            <prop key="hibernate.dialect">
                    org.hibernate.dialect.MySQLDialect
                </prop>
                <prop key="hibernate.show_sql">true</prop>
                <prop key="hibernate.format_sql">true</prop>
            </props>
        </property>
        <!-- 注入Hibernate的映射文件 -->
        <property name="mappingResources">
            <list>
                <value>com/py/beans/Users.hbm.xml</value>
                <value>com/py/beans/Customer.hbm.xml</value>
                <value>com/py/beans/Role.hbm.xml</value>
                <value>com/py/beans/Info.hbm.xml</value>
                <value>com/py/beans/Electric.hbm.xml</value>
                <value>com/py/beans/Payment.hbm.xml</value>
                <value>com/py/beans/Ammeter.hbm.xml</value>
                <value>com/py/beans/AmmeterType.hbm.xml</value>
            </list>
        </property>
    </bean>
    <!-- 事务管理器 -->
    <bean id="transactionManager"
        class="org.springframework.orm.hibernate3.HibernateTransactionManager" p:sessionFactory-ref="sessionFactory"/>
        <tx:annotation-driven transaction-manager="transactionManager"/>
        <tx:advice id="txAdvice" transaction-manager="transactionManager">
            <tx:attributes>
                <tx:method name="insert*" propagation="REQUIRED" />
                <tx:method name="update*" propagation="REQUIRED" />
                <tx:method name="edit*" propagation="REQUIRED" />
                <tx:method name="save*" propagation="REQUIRED" />
                <tx:method name="add*" propagation="REQUIRED" />
                <tx:method name="new*" propagation="REQUIRED" />
                <tx:method name="set*" propagation="REQUIRED" />
            </tx:attributes>
        </tx:advice>
        <aop:config>
            <aop:pointcut id="mypointcut" expression="execution(* com.**.biz..*.*(..))" />
            <aop:advisor advice-ref="txAdvice" pointcut-ref="mypointcut" />
        </aop:config>

</code>

* 项目有多个模块时，可以分别新建配置，比如新建applicationContext-users.xml中set设值注入 

<code>
    
    <bean id="usersdao" class="com.py.dao.impl.UsersDaoImpl">
        <property name="sessionFactory" ref="sessionFactory"></property>
    </bean>
    <bean id="usersbiz" class="com.py.biz.impl.UsersBizImpl">
        <property name="usersDao" ref="usersdao" ></property>   
    </bean>
    <!-- 原型protoype 每次注入或者通过spring应用上下文获取的时候，都会创建一个新的bean实例-->
    <bean id="usersaction" class="com.py.action.UsersAction" scope="prototype">
        <property name="usersBiz" ref="usersbiz"></property>    
        <property name="customerBiz" ref="customerbiz"></property>  
        <property name="ammeterBiz" ref="ammeterbiz"></property>    
    </bean>
</code>

* 在web.xml配置过滤器

<code>
    
    <!-- web的监听-->
    <listener>
        <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
    </listener>
    <context-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>/WEB-INF/applicationContext.xml,/WEB-INF/applicationContext-*.xml</param-value>
    </context-param>
    <!-- Struts2过滤器-->
    <filter>
        <filter-name>struts2</filter-name>
        <filter-class>org.apache.struts2.dispatcher.ng.filter.StrutsPrepareAndExecuteFilter</filter-class>
    </filter>
    <filter-mapping>
        <filter-name>struts2</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>
</code>

* struts.xml里面引用模块的xml,比如struts-users.xml

<code>
    
    <struts> 
    <include file="struts-users.xml"></include>
    <include file="struts-customer.xml"></include>
    <include file="struts-info.xml"></include>
    <include file="struts-payment.xml"></include>
    </struts>
</code>

<code>
    
    <package name="users" extends="struts-default" namespace="">
        <action name="logbyusername" class="usersaction" method="logByUsernameAndPwd">
            <result name="success">person/logBySidSucc.jsp</result>
        </action>
        <action name="logadmin" class="usersaction" method="login">
            <result name="toAdmin">index.jsp</result>
            <result name="toMaster">index2.jsp</result>
            <result name="toCustomer">person/index.jsp</result>
            <result name="toLogin">checkin.jsp</result>
        </action>
        <action name="userlogout" class="usersaction" method="logout">
            <result name="toLogin">../checkin.jsp</result>
        </action>
        <action name="listallusers" class="usersaction" method="listAllUsers">
        <result name="success">jsp/system_account.jsp</result>
        </action>
        
        <action name="addAddress" class="usersaction" method="addNewAddress">
            <result name="success">jsp/third.jsp</result>
            <result name="error">jsp/third.jsp</result>
        </action>
        <action name="deleteUser" class="usersaction" method="deleteUser">
            <result name="success">jsp/system_account.jsp</result>
        </action>
        
        <action name="userInfo" class="usersaction" method="userInfo">
            <result name="success">jsp/user-info.jsp</result>
        </action>
        <action name="toUserInfo" class="usersaction" method="userInfo">
            <result name="success">jsp/change-user-info.jsp</result>
        </action>
        <action name="changeUserInfo" class="usersaction" method="changeUserInfo">
            <result name="success">jsp/change-user-info-success.jsp</result>
        </action>
    </package>

</code>

* UserAction中的方法

<code>
    
    // 登录检查
    public String login() throws SQLException {
        Users u = this.usersBiz.logByUsernameAndPwd(this.users);
        if (u.getUsername().equals("") || u.getPassword().equals("")) {
            return "toLogin";
        } else if
        // 判断有无此人
        (usersBiz.logByUsernameAndPwd(u).getId() != 0) {
            u = usersBiz.logByUsernameAndPwd(u);
            int n = -1;
            if (u == null) {
                msg = "登录失败，请重新登录";
            } else {
                ActionContext.getContext().getSession().put("curr_user", u);
                n = usersBiz.logByUsernameAndPwd(u).getRole().getRid();
                switch (n) {
                case 1:
                    msg = "toAdmin";
                    break;
                case 3:
                    msg = "toMaster";
                    break;
                case 2:
                    msg = "toCustomer";
                    break;
                default:
                    ActionContext.getContext().put("logmsg", "登录失败，请重新登录");
                }
            }
        }
        return msg;
    }
    // 退出
    public String logout() {
        ActionContext.getContext().getSession().put("curr_user", null);
        return "toLogin";
    }
    public String listAllUsers() throws Exception {
        @SuppressWarnings("rawtypes")
        List list = this.usersBiz.listAll();
        ActionContext.getContext().put("list", list);
        return "success";
    }
    public String deleteUser(){
          this.usersBiz.deleteUserById(users.getId());
          return "success";
      }
      public String userInfo(){
          Users u = this.usersBiz.findById(users.getId());
          if(u.getCid()!=null){
             Customer c = this.customerBiz.findByCid(u.getCid());
             ActionContext.getContext().put("customer", c);
          }
          ActionContext.getContext().put("userInfo", u);
          return "success";
      }
      
      public String changeUserInfo(){
          Users u = this.usersBiz.findById(users.getId());
          u.setRole(users.getRole());
          u.setUsername(users.getUsername());
          this.usersBiz.updateUser(u);
          return "success";
      }
    // 新增地址信息
    public String addNewAddress() throws Exception {
        // 获取前端页面的地址信息
        // 用工具类里的函数进行拼接处理
        String[] str = { location_p, location_c, location_a, location_4,
                location_5 };
        String S = DealwithString.merge(str);
    
        Ammeter ammeter = new Ammeter();
        // 然后添加到数据库里
        ammeter.setAddress(S);
        ammeter.setAmmeterType(ammeterType);
        ammeter.setRemain(0.00);
        String s = ammeterBiz.addANewAddress(ammeter);
        
        if (s == null) {
            ActionContext.getContext().put("logmsg", "新增地址失败");
            return ERROR;
        }
        return SUCCESS;
    }

</code>

* 业务逻辑层实现接口

<code>
    
    public class UsersBizImpl implements UsersBiz{
    private UsersDao usersDao;
    public UsersDao getUsersDao(){
    return this.usersDao;
    }
    public void setUsersDao(UsersDao usersDao) {
    this.usersDao = usersDao;
    }
    public Users logByUsernameAndPwd(Users users) {
    String strHQL = "from Users u where u.username=? and u.password=? and u.role.rid=?";
    Object[] params = { users.getUsername(), users.getPassword(), users.getRole().getRid() };
    List list = this.usersDao.findByHQL(strHQL, params);
    if (list.size() == 0) {
      return null;
    }
    return ((Users)this.usersDao.findByHQL(strHQL, params).get(0));
    }
    public List<Users> listAll() {
    return this.usersDao.findAll();
     }
    public void deleteUserById(Integer id) {  
    }
    public Users findById(Integer id) {
    // TODO Auto-generated method stub
    return this.usersDao.findById(id);
    }
    public void updateUser(Users u) {
    // TODO Auto-generated method stub
    
    }
</code>

* dao层实现接口

<code>
    
    public class AGenericHibernateDao <T extends Serializable, ID extends Serializable >
        extends HibernateDaoSupport implements GenericDao<T, ID>{
    
    private Class<T> persistentClass;
    @SuppressWarnings("unchecked")
    public AGenericHibernateDao() {
        this.persistentClass = (Class<T>) ((ParameterizedType) this.getClass()
                .getGenericSuperclass()).getActualTypeArguments()[0];
    }
    
    @SuppressWarnings("unchecked")
    public ID insert(T entity) {
        return (ID) this.getHibernateTemplate().save(entity);
    }
    public T findById(ID id) {
        return this.getHibernateTemplate().get(persistentClass, id);
    }
    public void delete(ID id) {
        this.getHibernateTemplate().delete(findById(id));
    }
    public void update(T entity) {
        this.getHibernateTemplate().update(this.getHibernateTemplate().merge(entity));
    }
    public List<T> findAll() {
        return this.getHibernateTemplate().loadAll(persistentClass);
    }
    @SuppressWarnings("unchecked")
    public List<T> findByHQL(String strHQL, Object[] params) {
    //      Query query = super.getSession().createQuery(strHQL);
    //      if(params!=null&&params.length>0){
    //          for (int i = 0; i < params.length; i++) {
    //              query.setParameter(i, params[i]);
    //          }
    //      }
    //          return query.list();
        List query = this.getHibernateTemplate().find(strHQL, params);
        return query;
    } 
    public PageBean findByPage(final int currentPage, final int pageSize, final String strHQL,
            final Object[] params) {
        return this.getHibernateTemplate().execute(
                new HibernateCallback<PageBean>() {
                    public PageBean doInHibernate(Session arg0)
                            throws HibernateException, SQLException {
                        PageBean bean = new PageBean();
                        Query qu = arg0.createQuery(strHQL);
                        if(params!=null&&params.length>0){
                        for (int i = 0; i < params.length; i++) {
                            qu.setParameter(i, params[i]);
                        }
                        }
                        qu.setFirstResult((currentPage - 1) * pageSize);
                        qu.setMaxResults(pageSize);
                        bean.setData(qu.list());
                        qu = arg0.createQuery("select count(*) "
                                + strHQL.substring(strHQL.toLowerCase()
                                        .indexOf("from")));
                        if(params!=null&&params.length>0){
                        for (int j = 0; j < params.length; j++) {
                            qu.setParameter(j, params[j]);
                        }
                        }
                        bean.setTotalRows(Integer.parseInt(qu.uniqueResult()
                                .toString()));
                        bean.setCurrentPage(currentPage);
                        bean.setPageSize(pageSize);
                        return bean;
                    }
                });
    }
    public void bulkUpdate(String strHQL, Object[] params) {
        // TODO Auto-generated method stub
        
    }
    @Resource(name="sessionFactory")
    public void setSuperSessionFactory(SessionFactory sessionFactory) {
        super.setSessionFactory(sessionFactory);
    }
    public T insertO(T entity) {
        return (T) this.getHibernateTemplate().save(this.getHibernateTemplate().merge(entity));
    }
      public T findByAddress(T entity)
      {
        return getHibernateTemplate().get(this.persistentClass, entity);
      }
</code>

## 整个demo在我的GitHub：

* https://github.com/pugongyingbo/ssh