# TriggerYoutube (Apex trigger to send email to account owner)

public class AccountTriggerHandler {
    public static void handleAfterInsert(List<Account> accountList){
        
        List<Task> taskRecordToInsertList = new List<Task>(); 
        List<Messaging.SingleEmailMessage> emailMessages = new List<Messaging.SingleEmailMessage> (); //list to send email outside for loop
        for(Account acc : accountList){
            //prepare task record
            if(acc.Phone == Null && acc.Industry == Null)
            {
                //req #1 create task
                Task t = new Task();
                t.Subject = 'The Phone and Industry of account are blank';
                t.Description = 'The Phone and Industry of account are blank';
                t.Priority = 'High';
                t.Status = 'Not Started';
                t.WhatId = acc.Id;
                t.OwnerId = acc.OwnerId;
                taskRecordToInsertList.add(t);
                
                  //Req #2 send email
           Messaging.SingleEmailMessage email = new Messaging.SingleEmailMessage();
            email.setSubject('New Account has benn assigned');
            
           
           email.setPlainTextBody('emailBody');
            
            List<String> toAddress = new List<String>();
            toAddress.add('aaakash2sky@gmail.com');
            email.setToAddresses(toAddress);
            
            List<String> bccAddress = new List<String>();
            bccAddress.add('akashg@salesforce.com');
            email.setBccAddresses(bccAddress);
              //now we need to create a list to send email but we are inside for loop which create issue as email has limition
              //so create a list outside the for loop just above it 
        
        
        emailMessages.add(email); //add email to list
            
           List<Messaging.SendEmailResult> sendEmailResults = Messaging.sendEmail(emailMessages, false);
        for(Messaging.SendEmailResult sr : sendEmailResults){
            boolean isSuccess = sr.isSuccess();
            if(isSuccess){
                system.debug('email sent successfully');
            }else{
                system.debug('email not delivered');
            }
        }  
                
            }
          
        
        }
        
    }

}
