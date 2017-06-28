## Module 15: Handling Requests in ASP.NET MVC 4 Web Applications

## Lab: Handling Requests in ASP.NET MVC 4 Web Applications

#### Scenario

The Adventures Works board and managers are pleased with the Photo Sharing application, but have requested that interactivity should be maximized to encourage users to register and participate fully in the community. Therefore, you have been asked to add chat functionality to the application. Authenticated members should be able to start a chat on a particular photo from the Display view. Chat rooms for each photo should be separated from each other. Users in the chat room should be able to send a message to all other users in that chat room, and they should be able to see all the messages that have been sent since they joined the chat room.

#### You have decided to use SignalR to implement the chat room over Web Sockets. Objectives

After completing this lab, you will be able to:

- Install SignalR in an ASP. NET MVC 4 web application.
- Configure SignalR on the server and create a SignalR hub.
- Link to the required script files for SignalR in an MVC view.
- Create the script for SignalR connections and send messages to groups.

#### Lab Setup

Estimated Time: **60 minutes**

Virtual Machine: **20486B-SEA-DEV11**

User Name: **Admin**

Password: **Pa$$w0rd**

  >**Note:** In Hyper-V Manager, start the **MSL-TMG1** virtual machine if it is not already running.

Before initiating this lab, perform the following steps:

1. Apply the snapshot of the virtual machine, **20486B-SEA-DEV11**, that was taken after completing the lab in module 13.

2. Navigate to **Allfiles(D):\Mod13\Labfiles\Starter\PhotoSharingApplication\PhotoSharingApplication**, and then copy the **Web.config** file.
3. Navigate to **Allfiles(D):\Mod15\Labfiles\Starter\PhotoSharingApplication\PhotoSharingApplication**, and then paste the **Web.config** file.
4. Enable the **Allow NuGet to download missing packages during build** option by performing the following steps:

  a. On the **TOOLS** menu of the Microsoft Visual Studio window, click **Options**.  
  b. In the navigation pane of the **Options** dialog box, click **Package Manager**.  
  c. Under the Package Restore section, select the **Allow NuGet to download missing packages during build** check box, and then click **OK**.

### Exercise 1: Creating a SignalR Hub

#### Scenario

Before you can write JScript code on the client to connect to SignalR, you must configure and code a SignalR hub on the web server.

In this exercise, you will:

- Install SignalR in the Photo Sharing application.
- Configure routing.
- Create a SignalR hub to accept messages from clients and forward those messages to other clients who are chatting about the same photo.

The main tasks for this exercise are as follows:

1. Install SignalR.

2. Create a Hub class.

3. Configure SignalR routes.
 
#### Task 1: Install SignalR.

1. Start the **20486B-SEA-DEV11** virtual machine, and sign in with the following credentials:

   - User name: **Admin**
   - Password: **Pa$$w0rd**

2. Open the PhotoSharingApplication.sln file from the following location:

   - File location: **Allfiles(D):\Mod15\Labfiles\Starter\PhotoSharingApplication**

3. Use **NuGet Package Manager Console** to install the following package in the **PhotoSharingApplication** project:

   - **Microsoft.AspNet.SignalR version 1.0.0-rc2**

4. Notice the additions that the NuGet package made to the project references and the **Scripts** folder.

#### Task 2: Create a Hub class.

1. Add a new class file named **ChatHub.cs** to **PhotoSharingApplication**.

2. Remove the following namespace references from the **ChatHub.cs** class file:

   - **System.Collections.Generic;**
   - **System.Linq;**

3. Add the following namespace references to the **ChatHub.cs** class file:

   - **System.Threading.Tasks**
   - **Microsoft.AspNet.SignalR**

4. Ensure that the **ChatHub** class inherits from the **Microsoft.AspNet.SignalR.Hub** class.
5. In the **ChatHub** class, create a new method by using the following information:

   - Scope: **public**
   - Return type: **Task**
   - Name: **Join**
   - Parameter: an integer named **photoId**

6. In the **Join** method, return the result of the **Groups.Add()** method by using the following information:

   - Connection ID: **Context.ConnectionId**
   - Group name: **&quot;Photo&quot; + photoId**

7. In the **ChatHub** class, create a new method by using following information:

   - Scope: **public**
   - Return type: **Task**
   - Name: **Send**
   - First parameter: a string named **username**
   - Second parameter: an integer named **photoId**
   - Third parameter: a string named **message**

8. In the **Send** method, create a new **string** variable named **groupName** and set the value to **&quot;Photo&quot; + photoId**.
9. In the **Send** method, return the result of the **addMessage** method for the **groupName** group by using the following information:

   - Method: **Clients.Group(groupName).addMessage()**
   - User name: **username**
   - Message: **message**

10. Save all the changes.

#### Task 3: Configure SignalR routes.

1. In the **Global.asax** code-behind file, add a reference to the **Microsoft.AspNet.SignalR** namespace.

2. In the **Application\_Start()** method, immediately after the **RegisterAllAreas()** code, call the **RouteTable.Routes.MapHubs()** method.
3. Save all the changes.

  >**Results** : After completing this exercise, you should have successfully installed SignalR in an MVC 4 web application, configured routes for SignalR, and created a hub to receive and forward simple text messages.

### Exercise 2: Creating a Photo Chat View

#### Scenario

Now that you have set up and configured SignalR and a SignalR hub on the server side, you must use JScript and the SignalR JScript library to send and receive messages on the client side.

In this exercise, you will:

- Create a new MVC controller action and Razor view to display the chat user interface for a particular photo.
- Link to the JScript libraries that SignalR requires and write a client-side script to call the **Join()** and **Send()** methods on the hub.
- Test the chat functionality.

The main tasks for this exercise are as follows:

1. Create a chat action and view.

2. Link to the chat view.

3. Link to JScript files.

4. Script SignalR connections.

5. Script SignalR messages.

6. Test the chat room.

#### Task 1: Create a chat action and view.

1. Add a new action to the **PhotoController** class by using the following information:

   - Annotation: **[Authorize]**
   - Scope: **public**
   - Return type: **ActionResult**
   - Name: **Chat**
   - Parameter: an integer named **id**

2. Create a new **Photo** object named **photo** and get the **photo** value by passing the **id** parameter to the **context.FindPhotoById()** method.
3. If the **photo** object is null, return an HTTP Not Found status code.
4. At the end of the action, return the **Chat** view and pass the **photo** object as the model class.
5. Add the following view file to the **Views/Photo** folder:

   - **Allfiles(D):\Mod15\Labfiles\Chat View\Chat.cshtml**

6. Save all the changes.

#### Task 2: Link to the chat view.

1. In the **Display.cshtml** view file, after the **DIV** element with the ID **addtofavorites**, add a new **DIV** element, with the ID **chataboutthisphoto**.

2. In the new **DIV** element, render a link to the **Chat** view by using the following information:

   - Helper: **Html.ActionLink()**
   - Text: **Chat about this photo**
   - View: **Chat**
   - Route values: pass the **Model.PhotoID** value to the **id** parameter.

3. Start the web application in the debugging mode, display a photo of your choice, and then click **Chat**.
4. Sign in with the following credentials:

   - User name: **David Johnson**
   - Password: **Pa$$w0rd2**

5. Attempt to send a chat message.
6. Stop debugging.

#### Task 3: Link to JScript files.

1. Add a new **SCRIPT** element at the end of the **Chat.cshtml** view file, with type **text/javascript**.

2. In the new **SCRIPT** element, create a new variable named **username**. In a new Razor code block, use the **User.Identity.Name** property to set the value for the variable.
3. Create a second new variable named **photoid** and use the **Model.PhotoID** property to set the value for the variable.
4. Add a new **SCRIPT** element, with type **text/javascript**, and an empty **src** attribute.
5. In the new script element, use the **Url.Content()** helper to set the **src** attribute to the following path:

   - ~/Scripts/jquery.signalR-1.0.1.js

  >**Note:** Ensure that the name of the script file you enter matches the name of the file in the **Scripts** folder.

6. Add a new **SCRIPT** element, with type **text/javascript**, and an empty **src** attribute.
7. In the new script element, use the **Url.Content()** helper to set the **src** attribute to the following path:

   - ~/signalr/hubs

8. Add a new **SCRIPT** element, with type **text/javascript**, and an empty **src** attribute.
9. In the new script element, use the **Url.Content()** helper to set the **src** attribute to the following path:

   - ~/Scripts/ChatRoom.js

10. Save all the changes.

#### Task 4: Script SignalR connections.

1. Add a new JScript file named **ChatRoom.js** to the **Scripts** folder.

2. In the new JavaScript file, use jQuery to create an anonymous function that runs when the page loads.
3. Use the **$.connection.chatHub** property to obtain the **ChatHub** you already created, and then store the hub in a variable named **chat**.
4. Use jQuery to set the initial focus on the element with the ID **chat-message**.
5. Create an anonymous function that runs when the **$.connection.hub.start()** method is done.
6. Call the **chat.server.join()** function on the SignalR hub, pass the **photoid** value as a parameter, and then create an anonymous function that runs when the function is done.
7. Save all the changes.

#### Task 5: Script SignalR messages.

1. In the **ChatRoom.js** JScript file, after creating and instantiating the **chat** variable, set the **chat.client.addMessage** property to be a new anonymous function with two parameters, **name** and **message**.

2. In the new function, create a variable named **encodedName**, and use jQuery to set this variable to a new **&lt;div&gt;** with the **name** parameter as its HTML content.
3. In the new function, create a variable named **encodedMessage**, and use jQuery to set this variable to a new **&lt;div&gt;** with the **message** parameter as its HTML content.
4. Create a new variable named **listItem**. Set the value of this variable to an HTML **LI** element that includes the **encodedName** and **encodedMessage** variables.
5. Append the **listItem** element to the page element with the ID **discussion**.
6. In the function that runs when the **client.server.join()** method is done, create an anonymous function that runs when the button with the ID **sendmessage** is clicked.
7. In the new anonymous function, call the **chat.server.send()** method by using the following information:

   - User name: **username**
   - Photo ID: **photoid**
   - Message: use the value of the element with the ID **chat-message**

8. Use jQuery to obtain the element with the ID **chat-message**, set its value to an empty string, and give it the focus.
9. Save all the changes.

#### Task 6: Test the chat room.

1. Start the web application in the debugging mode and sign in with the following credentials:

   - User name: **David Johnson**
   - Password: **Pa$$w0rd2**

2. Browse to the chat page for **Sample Photo 1**.
3. Send a message of your choice and observe the results.
4. Start a new instance of Internet Explorer, and browse to the Photo Sharing application home page.
5. Register a new user account with the following credentials:

   - User name: **Mark Steele**
   - Password: **Pa$$w0rd**

6. Browse to the chat page for **Sample Photo 1**, and then send a message of your choice.
7. Switch to the first instance of Internet Explorer, and then send a second message of your choice. Observe the messages sent between the two users.
8. Stop debugging and close Visual Studio.

   >**Results** : After completing this exercise, you should have successfully created MVC controller actions and views to display a user interface for the SignalR functionality, linked to the JScript libraries that SignalR requires, and used JScript to connect to a SignalR hub and send messages.
   
©2016 Microsoft Corporation. All rights reserved.

The text in this document is available under the  [Creative Commons Attribution 3.0 License](https://creativecommons.org/licenses/by/3.0/legalcode), additional terms may apply. All other content contained in this document (including, without limitation, trademarks, logos, images, etc.) are  **not**  included within the Creative Commons license grant. This document does not provide you with any legal rights to any intellectual property in any Microsoft product. You may copy and use this document for your internal, reference purposes.

This document is provided &quot;as-is.&quot; Information and views expressed in this document, including URL and other Internet Web site references, may change without notice. You bear the risk of using it. Some examples are for illustration only and are fictitious. No real association is intended or inferred. Microsoft makes no warranties, express or implied, with respect to the information provided here.
