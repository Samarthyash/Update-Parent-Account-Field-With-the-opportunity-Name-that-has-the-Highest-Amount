//Trigger Class 

trigger OpportunityTrigger on Opportunity (After insert , After update , After delete) {
    if(Trigger.isInsert){
        if(Trigger.isAfter){
            OpportunityTriggerHandler.HighestOpportunityName(Trigger.new);
        }
    }
    if(Trigger.isUpdate){
        if(Trigger.isAfter){
            OpportunityTriggerHandler.HighestOpportunityName(Trigger.new);
        }
    }
    if(Trigger.isDelete){
        if(Trigger.isAfter){
            OpportunityTriggerHandler.HighestOpportunityName(Trigger.old);
        }
    }
}

//Handler Class 

public class OpportunityTriggerHandler {
  public static void HighestOpportunityName(List<Opportunity> oppList){
        Set<Id> accIds = new Set<Id>();
        for(Opportunity Opp : oppList){
            accIds.add(Opp.AccountId);
        }
        List<Account> accList =  new List<Account>();
        for(Account acc : [SELECT Id , Description , (SELECT AccountId , Name , Amount FROM Opportunities ORDER BY Amount DESC LIMIT 1 ) FROM Account WHERE Id IN : accIds]){
            for(Opportunity Opp : acc.Opportunities){
                acc.Description =  Opp.Name;
                accList.add(acc);
            }
        }
        if(accList.size()>0){
            update accList;
        }
    }
}
