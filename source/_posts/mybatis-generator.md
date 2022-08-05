## mbg-plugins
### 1. 插件列表
+ LoadPlugin:mapper.xml里面多个load方法
+ LombokPlugin：实体类里面支持lombok
+ MapperSqlPlugin:mapper.xml里面<sql></sql>
+ OverIsMergeablePlugin:是否覆盖已经存在的文件
+ QueryPlugin：mapper.xml里面 <select id="query"></select>

### 2.安装插件
插件地址：https://github.com/gaoqiuling/mbg-plugins
打包成jar包安装到maven本地仓库中

### 3.使用
#### pom中配置如下：

```
   <plugin>
                <groupId>org.mybatis.generator</groupId>
                <artifactId>mybatis-generator-maven-plugin</artifactId>
                <version>1.3.6</version>
                <configuration>
                    <configurationFile>
                        ${basedir}/src/main/resources/generator/generatorConfig.xml
                    </configurationFile>
                    <overwrite>true</overwrite>
                    <verbose>true</verbose>
                </configuration>
                <dependencies>
                    <dependency>
                        <groupId>mysql</groupId>
                        <artifactId>mysql-connector-java</artifactId>
                        <version>5.1.47</version>
                    </dependency>
                    <dependency>
                        <groupId>com.itisacat.generate</groupId>
                        <artifactId>generator-plugins</artifactId>
                        <version>0.0.1-SNAPSHOT</version>
                    </dependency>
                    <dependency>
                        <groupId>tk.mybatis</groupId>
                        <artifactId>mapper</artifactId>
                        <version>4.0.0</version>
                    </dependency>
                </dependencies>
            </plugin>
        </plugins>

```

#### 配置generatorConfi.xml
在src/main/resources/generator目录下创建generatorConfig.xml，配置如下：
```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE generatorConfiguration
        PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
        "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">

<generatorConfiguration>

    <context id="mysql" targetRuntime="MyBatis3Simple" defaultModelType="flat">
        <property name="beginningDelimiter" value="`"/>
        <property name="endingDelimiter" value="`"/>

        <plugin type="tk.mybatis.mapper.generator.MapperPlugin">
            <property name="mappers" value="tk.mybatis.mapper.common.Mapper,tk.mybatis.mapper.common.MySqlMapper"/>
            <property name="caseSensitive" value="true"/>
        </plugin>
        <!-- 实体使用lombok注解 -->
        <plugin type="com.itisacat.generate.plugin.LombokPlugin"/>
        <!-- xml覆盖 -->
        <plugin type="com.itisacat.generate.plugin.OverIsMergeablePlugin"/>
        <!-- mapper.xml里面sql -->
        <plugin type="com.itisacat.generate.plugin.MapperSqlPlugin"/>
        <!-- mapper.xml里面load方法-->
        <plugin type="com.itisacat.generate.plugin.LoadPlugin"/>
        <!-- mapper.xml里面queryList方法-->
        <plugin type="com.itisacat.generate.plugin.QueryPlugin"/>
        <!-- 实体及属性使用自定义注解 -->
        <commentGenerator type="com.spring.mybatis.plugin.CustomCommentGenerator">
            <property name="suppressDate" value="true"/>
            <property name="suppressAllComments" value="false"/>
            <property name="javaFileEncoding" value="UTF-8"/>
        </commentGenerator>
        <jdbcConnection driverClass="com.mysql.jdbc.Driver"
                        connectionURL="jdbc:mysql://localhost:3306/test?useSSL=false"
                        userId="root"
                        password="">
        </jdbcConnection>

        <javaTypeResolver>
            <property name="forceBigDecimals" value="false"/>
        </javaTypeResolver>

        <!-- 生成实体的位置 -->
        <javaModelGenerator targetPackage="com.itisacat.demo.support.po" targetProject="src/main/java">
            <property name="enableSubPackages" value="true"/>
            <property name="trimStrings" value="true"/>
        </javaModelGenerator>
        <!-- 生成 MapperXML的位置 -->
        <sqlMapGenerator targetPackage="/" targetProject="src/main/resources/mapper/">
            <property name="enableSubPackages" value="true"/>
        </sqlMapGenerator>
        <!-- 生成 Mapper接口的位置 -->
        <javaClientGenerator type="XMLMAPPER" targetPackage="com.itisacat.demo.dao"
                             targetProject="src/main/java">
            <property name="enableSubPackages" value="true"/>
        </javaClientGenerator>
        <table tableName="t_operation_log">
        </table>
    </context>
</generatorConfiguration>

```


### 4.执行maven插件:mybatis-generator
生成代码如下：
```
实体类:
@Table(name = "t_operation_log")
@Data
@Builder
public class TOperationLog {
    @Id
    private Integer id;

    /**
     * 关联id
     */
    @Column(name = "ref_id")
    private Long refId;

    /**
     * 模块名称
     */
    @Column(name = "module_name")
    private String moduleName;

    /**
     * 标题
     */
    private String title;

    /**
     * 内容
     */
    private String content;

    /**
     * ip地址
     */
    private String ip;

    /**
     * 详细信息
     */
    @Column(name = "detail_desc")
    private String detailDesc;

    /**
     * 操作人
     */
    @Column(name = "operate_user_id")
    private Integer operateUserId;

    /**
     * 创建时间
     */
    @Column(name = "create_time")
    private Date createTime;

    /**
     * 更新时间
     */
    @Column(name = "update_time")
    private Date updateTime;

    @Transient
    private List<Pair<String, String>> orderFields = Lists.newArrayList();

    /**
     * @return id
     */
    public Integer getId() {
        return id;
    }

    /**
     * @param id
     */
    public void setId(Integer id) {
        this.id = id;
    }

    /**
     * 获取关联id
     *
     * @return ref_id - 关联id
     */
    public Long getRefId() {
        return refId;
    }

    /**
     * 设置关联id
     *
     * @param refId 关联id
     */
    public void setRefId(Long refId) {
        this.refId = refId;
    }

    /**
     * 获取模块名称
     *
     * @return module_name - 模块名称
     */
    public String getModuleName() {
        return moduleName;
    }

    /**
     * 设置模块名称
     *
     * @param moduleName 模块名称
     */
    public void setModuleName(String moduleName) {
        this.moduleName = moduleName == null ? null : moduleName.trim();
    }

    /**
     * 获取标题
     *
     * @return title - 标题
     */
    public String getTitle() {
        return title;
    }

    /**
     * 设置标题
     *
     * @param title 标题
     */
    public void setTitle(String title) {
        this.title = title == null ? null : title.trim();
    }

    /**
     * 获取内容
     *
     * @return content - 内容
     */
    public String getContent() {
        return content;
    }

    /**
     * 设置内容
     *
     * @param content 内容
     */
    public void setContent(String content) {
        this.content = content == null ? null : content.trim();
    }

    /**
     * 获取ip地址
     *
     * @return ip - ip地址
     */
    public String getIp() {
        return ip;
    }

    /**
     * 设置ip地址
     *
     * @param ip ip地址
     */
    public void setIp(String ip) {
        this.ip = ip == null ? null : ip.trim();
    }

    /**
     * 获取详细信息
     *
     * @return detail_desc - 详细信息
     */
    public String getDetailDesc() {
        return detailDesc;
    }

    /**
     * 设置详细信息
     *
     * @param detailDesc 详细信息
     */
    public void setDetailDesc(String detailDesc) {
        this.detailDesc = detailDesc == null ? null : detailDesc.trim();
    }

    /**
     * 获取操作人
     *
     * @return operate_user_id - 操作人
     */
    public Integer getOperateUserId() {
        return operateUserId;
    }

    /**
     * 设置操作人
     *
     * @param operateUserId 操作人
     */
    public void setOperateUserId(Integer operateUserId) {
        this.operateUserId = operateUserId;
    }

    /**
     * 获取创建时间
     *
     * @return create_time - 创建时间
     */
    public Date getCreateTime() {
        return createTime;
    }

    /**
     * 设置创建时间
     *
     * @param createTime 创建时间
     */
    public void setCreateTime(Date createTime) {
        this.createTime = createTime;
    }

    /**
     * 获取更新时间
     *
     * @return update_time - 更新时间
     */
    public Date getUpdateTime() {
        return updateTime;
    }

    /**
     * 设置更新时间
     *
     * @param updateTime 更新时间
     */
    public void setUpdateTime(Date updateTime) {
        this.updateTime = updateTime;
    }

    public TOperationLog orderById(boolean isAsc) {
        orderFields.add(new Pair<>("id", isAsc ? "ASC" : "DESC"));
        return this;
    }
}

dao:
public interface TOperationLogMapper extends Mapper<TOperationLog>, MySqlMapper<TOperationLog> {
    TOperationLog load(int id);

    List<TOperationLog> queryList(TOperationLog object);
}

xml:
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.bsd.intelligent.purchase.admin.dao.TOperationLogMapper">
  <resultMap id="BaseResultMap" type="com.bsd.intelligent.purchase.admin.support.po.TOperationLog">
    <!--
      WARNING - @mbg.generated
    -->
    <id column="id" jdbcType="INTEGER" property="id" />
    <result column="ref_id" jdbcType="BIGINT" property="refId" />
    <result column="module_name" jdbcType="VARCHAR" property="moduleName" />
    <result column="title" jdbcType="VARCHAR" property="title" />
    <result column="content" jdbcType="VARCHAR" property="content" />
    <result column="ip" jdbcType="VARCHAR" property="ip" />
    <result column="detail_desc" jdbcType="VARCHAR" property="detailDesc" />
    <result column="operate_user_id" jdbcType="INTEGER" property="operateUserId" />
    <result column="create_time" jdbcType="TIMESTAMP" property="createTime" />
    <result column="update_time" jdbcType="TIMESTAMP" property="updateTime" />
  </resultMap>
  <sql id="Base_Column_List">
    id,
    ref_id,
    module_name,
    title,
    content,
    ip,
    detail_desc,
    operate_user_id,
    create_time,
    update_time
  </sql>
  <sql id="listWhere">
    <where>
      <if test="id != null">
        AND id = #{id}
      </if>
      <if test="refId != null">
        AND ref_id = #{refId}
      </if>
      <if test="moduleName != null and moduleName !=''">
        AND module_name = #{moduleName}
      </if>
      <if test="title != null and title !=''">
        AND title = #{title}
      </if>
      <if test="content != null and content !=''">
        AND content = #{content}
      </if>
      <if test="ip != null and ip !=''">
        AND ip = #{ip}
      </if>
      <if test="detailDesc != null and detailDesc !=''">
        AND detail_desc = #{detailDesc}
      </if>
      <if test="operateUserId != null">
        AND operate_user_id = #{operateUserId}
      </if>
      <if test="createTime != null">
        AND create_time = #{createTime}
      </if>
      <if test="updateTime != null">
        AND update_time = #{updateTime}
      </if>
    </where>
  </sql>
  
  <sql id="listOrder">
    <if test="orderFields != null and orderFields.size > 0">
      ORDER BY
      <foreach collection="orderFields" item="item" separator=",">
        ${item.key} ${item.value}
      </foreach>
    </if>
  </sql>

  <sql id="query">
    select
    <include refid="Base_Column_List" />
    from t_operation_log
    <include refid="listWhere" />
    <include refid="listOrder" />
  </sql>
  <select id="load" resultMap="BaseResultMap">
    select
    <include refid="Base_Column_List" />
    from t_operation_log
    where id = #{id}
  </select>
  <select id="queryList" resultMap="BaseResultMap">
    <include refid="query" />
  </select>
</mapper>
```
