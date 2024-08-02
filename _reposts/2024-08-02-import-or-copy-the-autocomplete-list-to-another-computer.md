---
title: "Import or copy the AutoComplete list to another computer"
collection: reposts
permalink: /reposts/2024-08-02-import-or-copy-the-autocomplete-list-to-another-computer
excerpt: 'The AutoComplete list is a feature that displays suggestions for names and email addresses as you begin to type them.'
date: 2024-08-02
venue: 'Microsoft'
paperurl: 'https://support.microsoft.com/en-us/office/import-or-copy-the-autocomplete-list-to-another-computer-83558574-20dc-4c94-a531-25a42ec8e8f0'
citation: 'Import or copy the AutoComplete list to another computer - Microsoft Support. (n.d.). https://support.microsoft.com/en-us/office/import-or-copy-the-autocomplete-list-to-another-computer-83558574-20dc-4c94-a531-25a42ec8e8f0'
---


The AutoComplete list is a feature that displays suggestions for names and email addresses as you begin to type them. These suggestions are possible matches from a list of names and email addresses from the email messages that you have sent. 

![image](https://github.com/user-attachments/assets/c28ecf1f-b658-4e64-a5c9-b48bf2bf74c7)

The method to copy your AutoComplete list from one computer to another depends on what type of email account you've added to Outlook. If you have a Microsoft 365 account, Exchange Server account, or an IMAP account (this is the most common type of other email account), then the AutoComplete list is stored as a hidden file in your Outlook Data File. See [Copy the AutoComplete list]([url](https://support.microsoft.com/en-us/office/import-or-copy-the-autocomplete-list-to-another-computer-83558574-20dc-4c94-a531-25a42ec8e8f0#copylist)) for instructions.

If you have a POP3 account (less common, but often used for third-party email providers like Comcast, Earthlink, and Verizon), your AutoComplete list is stored in a file stored on your computer. See [Copy and import an .nk2 file]([url](https://support.microsoft.com/en-us/office/import-or-copy-the-autocomplete-list-to-another-computer-83558574-20dc-4c94-a531-25a42ec8e8f0#importnk2)).

If you don't know what type of account you have, select **File > Account Settings > Account Settings**. You can find your account type here.

## Copy the AutoComplete list

### Step 1: Export the AutoComplete mailbox message

1. Exit Outlook, and then close Outlook on the web on all workstations that are connected to your mailbox.
2. Download and install MFCMAPI from [http://mfcmapi.codeplex.com](https://github.com/microsoft/mfcmapi/releases).
3. Run [mfcmapi.exe]([url](https://github.com/mkolakowski/mkolakowski.github.io/blob/ccb147c51beae5a22f8c4f8e6f5b164d5128ff65/files/MFCMAPI.exe.24.0.24100.06.zip)).
4. On the **Session** menu, select **Logon**.
5. If you are prompted for a profile, select the desired profile name, and then click **OK**.
6. In the top pane, locate the line that corresponds to your mailbox, and then double-click it.
7. In the left-side navigation pane, expand **Root Container**, and then expand **Top of Information Store** or **IPM_SUBTREE**.
8. Right-click the Inbox folder, and then select **Open Associated Content Table**. This action opens a new MFCMAPI window that contains various properties.
9. Under the **Subject** column, right-click the item that has the subject, **IPM.Configuration.Autocomplete**, and then select **Export Message**. This action opens the **Save Message To File** window.
10. In the drop-down list, select **MSG file (UNICODE)**, and then select **OK**.
11. Select a folder location to which you want to save the message, and then select **Save**. Note this location.

### Step 2: Import the AutoComplete mailbox message
1. Exit Outlook, and then close Outlook on the web on all workstations that are connected to your mailbox.
2. Download and install MFCMAPI from [](https://github.com/microsoft/mfcmapi/releases).
3. Run mfcmapi.exe.
4. On the Session menu, select Logon.
5. If you are prompted for a profile, select the desired profile name, and then click **OK**.
6. In the top pane, locate the line that corresponds to your mailbox, and then double-click it.
7. In the left-side navigation pane, expand Root Container, and then expand **Top of Information Store** or **IPM_SUBTREE**.
8. Right-click the Inbox folder, and then select **Open Associated Content Table**. This action opens a new MFCMAPI window that contains various properties.
9. To avoid duplicate entries, you must delete the existing AutoComplete message.
   - **Note**: Before you delete the **IPM.Configuration.Autocomplete** message, you must export the message by using the steps in the "_How to export the Auto-Complete cache_" section.
   - To delete the existing AutoComplete message, follow these steps:
   - In the **Subject** column, locate the item that has the subject, **IPM.Configuration.Autocomplete**.
   - Right-click the item, and then select **Delete message**. This opens the **Delete Item** window.
   - In the drop-down list, select **Permanent deletion (deletes to deleted item retention if supported)**, and then select **OK**.
10. On the Folder menu, select **Import**, and then select **From MSG**.
11. Locate the .msg file that you created in step 11 of the "Export the AutoComplete mailbox message" procedure above, and then select **OK**.
12. In the **Load MSG** window that appears, select **Load message into current folder** in the **Load style** list, and then select **OK**.

   _The AutoComplete information is imported from the IPM.Configuration.Autocomplete_<hexadecimal code>.msg, where the placeholder<hexadecimal code> represents a long string of numbers and letters.
## Copy and import an .nk2 file

### Step 1: Copy the Auto-Complete file from the old computer
1. Because the default folder is hidden folder, the easiest way to open the folder is to use the command `%APPDATA%\Microsoft\Outlook` in the Windows Search box (or, browse to `C:\Users\username\AppData\Roaming\Microsoft\Outlook`).
2. In the Outlook folder, find your Auto-Complete List (.nk2) file.
   - **Note**: _By default, file extensions are hidden in Windows. To change whether file extensions are shown, in Window Explorer on the_ **Tools** _menu (in Windows 7 or Windows Vista, press the ALT key to see the_ **Tools** _menu), click_ **Folder Options**. _On the_ **View** _tab select or clear the_ **Hide extensions for known file types** _check box_.
3. Copy the file to the new computer. The file is small and can be placed on a removable media such as a USB memory stick.

### Step 2: Copy the Auto-Complete file to the new computer
1. On the new computer, in Control Panel, select or double-click **Mail**.
   - **Mail** appears in different Control Panel locations depending on the version of the Microsoft Windows operating system, the Control Panel view selected, and whether a 32- or 64-bit operating system or version of Outlook 2010 is installed.
   - The easiest way to locate **Mail** is to open Control Panel in Windows, and then in the **Search** box at the top of window, type **Mail**. In Control Panel for Windows XP, type **Mail** in the **Address** box.
   - **Note**: _The_ **Mail** _icon appears after Outlook starts for the first time_.
2. Select **Show Profiles**.
3. Make a note of the name of the profile. You will need to change name of the .nk2 file to match this name later.
4. Copy the .nk2 file to the new computer in the folder in which Outlook configurations are saved. Because the default folder is hidden folder, the easiest way to open the folder is to use the command `%APPDATA%\Microsoft\Outlook` in the Windows Search box (or, browse to `C:\Users\username\AppData\Roaming\Microsoft\Outlook`).
5. After the file is copied to the folder, right-click the file, click **Rename**, and change the name to match the profile name that you noted in step 3.

### Step 3: Import the Auto-Complete List
You are now ready to start Outlook and import the file, but you must start Outlook with a special one-time command.
- Type `outlook /importnk2` in the Windows **Search** box and then press Enter.
The Auto-Complete List should now have the entries from your other computer when you compose a message and begin typing in the **To**, **Cc**, or **Bcc** boxes.

