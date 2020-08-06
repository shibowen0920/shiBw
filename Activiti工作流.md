#### Activiti工作流

- pom的jar包

```xml
<properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <java.version>1.8</java.version>
    <activiti.version>5.21.0</activiti.version>
</properties>
<dependency>
   <groupId>org.activiti</groupId>
   <artifactId>activiti-spring-boot-starter-basic</artifactId>
   <version>${activiti.version}</version>
</dependency>
```

- activiti核心类

![img](https://img-blog.csdn.net/20160204114613838?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

repositoryService：activiti是不能直接识别xml格式的流程图的，流程图文件必须被部署到activiti中才能被activiti识别并使用。而这个过程就是使用repositoryService来完成的，流程图被导入后，会放到act_re_打头的几个表中。repositoryService提供了若干个接口，既可以部署xml文件到数据库中，也可以从数据库检索特定流程图供处理。

    runtimeService：activiti最重要的一个服务，基本上所有的关于流程的操作都是通过此服务来执行的。例如启动流程、审批、会签等等。
    
    formService：工作流的设计思路之一就是将每个节点需要显示的数据直接绑定到此节点。而formService就是专门为此服务的，使用formService可以获取某个节点绑定的表单数据。当然，如果没有表单绑定到此节点，此服务就没有任何用处。
    
    identityService：activiti自带的用于管理自身的组织机构的服务。activiti自身的组织机构包括user和group两大类，而user、group以及user和group的关系都是通过自服务来维护的。因此如果需要使用activiti自身的组织机构的，就会使用到此服务。不过通常情况下我们都是是用的框架中的身份认证和组织机构，所以此服务使用频率也不高。
    
    taskService：任务是activiti的核心功能之一，所有涉及到任务的操作都是通过此服务来完成的。例如任务的查询、分配、认领、完成等。
    
    historyService：所有流程实例的信息都会被保存的历史信息中，当一个流程实例结束之前，它是被保存在runtime和history两个地方，当它结束后，就只有history里了。
- demo

- 首先在resources中创建一个processes文件夹，avtiviti启动时会自动扫描里面的xml文件

- 新建一个bpmn文件，画流程，改为xml文件

- ```xml
  <?xml version='1.0' encoding='UTF-8'?>
  <definitions xmlns="http://www.omg.org/spec/BPMN/20100524/MODEL"
               xmlns:activiti="http://activiti.org/bpmn"
               typeLanguage="http://www.w3.org/2001/XMLSchema" expressionLanguage="http://www.w3.org/1999/XPath"
               targetNamespace="http://www.activiti.org/test">
     <process id="joinProcess" name="Join process" isExecutable="true">
        <startEvent id="startevent1" name="Start">
           <extensionElements>
              <activiti:formProperty id="personId" name="person id" type="long"
                                     required="true"></activiti:formProperty>
              <activiti:formProperty id="compId" name="company Id" type="long"
                                     required="true"></activiti:formProperty>
           </extensionElements>
        </startEvent>
        <endEvent id="endevent1" name="End"></endEvent>
        <userTask id="ApprovalTask" name="Approval Task"
                  activiti:candidateUsers="${joinService.findUsers(execution)}" isForCompensation="true">
           <extensionElements>
              <activiti:formProperty id="joinApproved" name="Join Approved" type="enum">
                 <activiti:value id="true" name="Approve"></activiti:value>
                 <activiti:value id="false" name="Reject"></activiti:value>
              </activiti:formProperty>
           </extensionElements>
        </userTask>
        <sequenceFlow id="flow1" sourceRef="startevent1" targetRef="ApprovalTask"></sequenceFlow>
        <serviceTask id="AutoTask" name="Auto Task"
                     activiti:expression="${joinService.joinGroup(execution)}"></serviceTask>
        <sequenceFlow id="flow2" sourceRef="ApprovalTask" targetRef="AutoTask"></sequenceFlow>
        <sequenceFlow id="flow3" sourceRef="AutoTask" targetRef="endevent1"></sequenceFlow>
     </process>
  </definitions>
  ```

- 创建对应的实体类

  - Comp，Person

- 生成一个流程实例（页面调用）

  - ```java
    //开始流程，传入申请者的id以及公司的id
    public void startProcess(Long personId, Long compId) {
       Map<String, Object> variables = new HashMap<String, Object>();
       variables.put("personId", personId);
       variables.put("compId", compId);
       runtimeService.startProcessInstanceByKey("joinProcess", variables);
    }
    ```

- 页面获取某人的任务列表

- ```java
  //获得某个人的任务列表
  public List<Task> getTasks(String assignee) {
     return taskService.createTaskQuery().taskCandidateUser(assignee).list();
  }
  ```

- 完成任务

- ```java
  //完成任务
  public void completeTasks(Boolean joinApproved, String taskId) {
     Map<String, Object> taskVariables = new HashMap<String, Object>();
     taskVariables.put("joinApproved", joinApproved);
     taskService.complete(taskId, taskVariables);
  }
  ```