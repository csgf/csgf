************
Creation of a simple parameterised portlet
************

This complete example of portlet code contains anything you need to develop your own first portlet . For this reason developers can use it as a template source code to customize according to their own specific requirements.
Following instructions provide a step by step guide explaining how to customize the template in order to obtain in the fastest possible way a full featured web application.

**Portlet Workflow**

Before to start with the portlet template it is important to understand the internal &nbsp;workflow of a standard portlet (JSR168/286). The picture below well depicts the entire workflow among the different portlet components. Components in the figure are simply class methods of the GenericPortlet Java class provided by the portlet SDK.

.. image:: figures-and-documents/figure1.png
   :align: left


::

	Class GenericPortlets{
	init(PortletConfig);
	processAction(ActionRequest, ActionResponse);
	render (RenderRequest, RenderResponse);
	destroy();
	**doView**(Request, Response);
	**doEdit**(Request, Response);
	**doHelp**(Request, Response);
	}

- The above figure depicts the whole lifecycle of a portlet; the most important loop foresees the exchange between the **ProcessAction** and **Render** methods, respectively responsible of the action selected by the user in the input forms and then the interface to show back to the user as consequence of the user action.

**Portlet Modes**
		
Standard portlets opreate in 3 different modes: **VIEW**, **EDIT**, **HELP**


* **VIEW**: generates the normal user interface
* **EDIT**: used to store portlet preferences
* **HELP**: show usage instruction

The **Render** method is responsible to call a different **GenericPortlet** class method accordingly to the current portlet mode as shown in the figure:

.. image:: figures-and-documents/figure2.png
   :align: left	
          	
		**Render** method will call then **GenericPortlet**methods: **doView** ,&nbsp;<strong>doHelp</strong>,&nbsp;<strong>doEdit</strong><br />
			Each method is responsible to present the appropriate user interface accordingly to the user action and portlet status.</p>



**Data Exchange between Java and JSP pages**

During the user interaction there is a continuous data exchange between the&nbsp;<strong>GenericPortlet</strong>&nbsp;class and the&nbsp;<strong>JSP</strong>&nbsp;pages responsible of the user interface presentation. Following paragraphs show how exchange data between jps pages and the Java code.</p>
			
JSP -> Java

Inside the JSP code place all JAVA' input fields into a web form:

::

    <form action="<portlet:actionURL portletMode="view"> 
    <portlet:param name="param_name_1" value="paramvalue 1" />
    ... 
    <portlet:param name="param_name_n" value="paramvalue n"/> 
    ...
    <input … />
    <input … />

    <input type="submit" … />
    </form>


Inside the JAVA code get the input interface values with:

::

    doView/doHelp/doEdit(RenderRequest  request,…
    // To obtain the parameter just set …
    String param_i= request.getParameter("param_name_i");


Java -> JSP

Inside the JAVA code get the input interface values with:

::
    
    doView/doHelp/doEdit(RenderRequest  request,…
    // To obtain the parameter just set …
    String param_i= request.setAttribute("param_name_i","param_value_i");


Inside the JSP page load parameter values with:

::
    
    <%
     // To load variables from PortletClass …
    %>
    <jsp:useBean id="param_name_k" class="<variable type k>" scope="request"/>
    
    <%
    // To reference a paramvalue
    %>

Reference paramenter_name' value with: <%=param_name_k%>

**GenericPortlet main workflow**
		
	The following picture shows the internal workflow inside the GenericPortlet class while the user interacts with the WebApplication:</p>
			
		.. image:: figures-and-documents/figure3.png
  		   :align: left	

The loop starts with the&nbsp;<strong>Init()</strong>&nbsp;method then the entire workflow plays around the methods&nbsp;<strong>ProcessAction</strong>&nbsp;and&nbsp;<strong>doView</strong>(assuming the VIEW mode). For each User Action a different View will be selected<br />
			During this loop two important object instances are used to exchange data between the&nbsp;<strong>doView</strong>&nbsp;and&nbsp;<strong>processAction&nbsp;</strong>methods as shown below:</p>
			
		.. image:: figures-and-documents/figure4.png
                   :align: left	

<strong>actionRequest</strong>&nbsp;input of processAction method which prepares the render object for view methods</p>
			<strong>renderRequest</strong>&nbsp;input of View methods:&nbsp;<strong>doView</strong>/<strong>doHelp</strong>/<strong>doEdit</strong></p>
		


**Deploy myFirstPortlet**

In this section we can see how the steps that you have to follow to deploy the&nbsp;<strong>myFirst-portlet</strong>&nbsp;in your liferay bundle installation.&nbsp;</p>

1. Move in your Liferay plugin SDK potlets folder

::
    
    cd $LIFERAY_SDK_HOME/portlets/

2. Download myFirst-portlet source code through svn command:

::
   
    svn checkout svn://svn.code.sf.net/p/ctsciencegtwys/liferay/trunk/gilda/myFirst-portlet

3. Move into myFirst-portlet/ folder

4. Deploy portlet with the following command (and see LIferay log):

::
   
   ant deploy

If built process complet successfully , you can see in Liferay Log somethins like this:

::

    Successfully autodeployed : LIFERAY_HOME/glassfish-3.1.2/domains/domain1/autodeploy/myFirst-portlet.|#

5. Open web browser at <a href="http://localhost:8080" target="_blank">http://localhost:8080</a>, click on Add &gt; More&gt; CataniaSG &gt; myFirst-portlet.
		
.. image:: figures-and-documents/figure5.png
   :align: left

**Customize myFirstPortlet**

This section describes the steps to create a new portlet from the template provided by myFirst-portlet.

- Move into Liferay plugin SDK portlets folder
- Copy myFirst-portlet folder in &lt;your_portlet_name&gt;-portlet <span style="font-family: 'Courier New', Courier, monospace; white-space: pre;">cp -R myFirst-portlet &lt;your_portlet_name&gt;-portlet</span>
- Move into&nbsp;&lt;your_portlet_name&gt;-portlet folder
- Edit the customize.sh file, set the following parameters as you prefer:
-- AUTH_EMAIL= your@email
-- AUTH_NAME= your name
-- AUTH_INSTITUE= your_institute

Pay attention: the APP_NAME value must be set with the name that you assigned in your portlet folder name

- APP_NAME=&lt;your_potlet_name
- Run customize.sh script, with <code>./customize.sh
- Then delpoy portlet with ant deploy
		
			
To see the result follow step 5 in previous section changing &nbsp;myFirst-portlet with &lt;your_potlet_name&gt;-portlet</p>
		
**Web application editors**

This is the right moment to create a project using a high level web application editor like NetBeans or Eclipse.
Following instructions are valid for **NetBeans**

- Download Netbeans IDE

- Open <em>New Project&nbsp;</em>&gt;&nbsp;<em>Java Web</em> &gt; <em>Web Application</em> <em>with</em> <em>Existing Sources </em>&nbsp;and press <strong>'Next'</strong>;

- In Location browse the&nbsp;&lt;your_potlet_name&gt;-portlet&nbsp;directory and press <strong>'Next'</strong>;

- Accept any suggestion and proceed and press <strong>'Next'</strong>;

- Add other directory places;
				<span style="font-family: Arial, Verdana, sans-serif;">WEB-INF Content: Select the docroot/WEB-INF directory inside the&nbsp;&lt;your_potlet_name&gt;-portlet&nbsp;directory;</span></li>

- Then press the&nbsp;</span><strong style="font-family: Arial, Verdana, sans-serif;">'Finish'</strong><span style="font-family: Arial, Verdana, sans-serif;">&nbsp;button and the project will be created;</span>

			<li>
				<div id="cke_pastebin" style="font-family: Arial, Verdana, sans-serif;">
					Right click on the project name and click on Peferences, then Libraries.</div>
				<div id="cke_pastebin" style="font-family: Arial, Verdana, sans-serif;">
					Select all jars pointed by&nbsp;<code>$LIFERAY_HOME/glassfish-3.1.2/domains/domain1/lib&nbsp;</code><span style="font-family: Arial, Helvetica, Verdana, sans-serif;">(</span><span style="font-family: Arial, Helvetica, Verdana, sans-serif;">in your liferay bundle).&nbsp;</span></div>
			</li>

Following instructions are valid for&nbsp;<strong>Eclipse</strong>

- Download Eclipse IDE for java EE Developers;

- Set the Eclipse Workspace to the "portlets"&nbsp;<span style="font-family: 'Courier New', Courier, monospace; white-space: pre;">$LIFERAY_SDK_HOME/portlets/ </span>directory;

- Select <em>File&nbsp;</em>&gt; <em>New</em>&nbsp;&gt; <em>Web</em> &gt;&nbsp;<em>Dynamic Web Porject</em> and press <strong>'Next'</strong>

Fill the Web Dynamic Web Project Wizard with </span>
						
- the project name: </span><span style="font-family: Arial, Verdana, sans-serif;">&lt;your_potlet_name&gt;-portlet;</span>
- the default location&nbsp;<span style="font-family: Arial, Verdana, sans-serif;">only if the default one is not correct;</span>
- the glassfish target runtime (if doesn't exist create a new one with the New Runtime... wizard);</span>
- leave the default values for Dynamic Web module_vesion and Configuration fields and press <strong>'Next'</strong>;</span>
- Change the Content Directory to "docroot";
- Change the Java Source Directory to "docroot/WEB-INF/src" and press <strong>'Finish'</strong>;</span>
- In order to fix some library dependencies could be necessary add external Jars.&nbsp;</span><span style="font-family: Arial, Verdana, sans-serif;">Right click on the project name and click on <em>Properties</em> &gt; <em>Java Build Path</em> &gt;<em> Libraries</em> and s</span><span style="font-family: Arial, Verdana, sans-serif;">elect all jars pointed by&nbsp;</span><code>$LIFERAY_HOME/glassfish-3.1.2/domains/domain1/lib&nbsp;</code>(in your liferay bundle).&nbsp;

Start to develop the interface modifying jsp files and change java code enums with correct Actions and Views modese with the proper identifiers. For simple user interfaces there will be no need to add other jsp or action/view modes.</span></p>

