import java.util.*;
import java.io.*;

// Class for patient data
// Class for patient data
class Patient
{
  private String patientId; 
  private boolean remission;
  private double remDuration;
  private double monthsToLive;
  private String infection;
  HashMap<String, Double> data = new HashMap<String, Double>();
  
  // Constructor that takes all Patient attributes as input
  public Patient(String patientId, String infection, boolean Remission, double RemDuration, double monthsToLive, HashMap<String, Double> data){
    this.patientId = patientId;
    this.remission = remission;
    this.infection = infection;
    this.remDuration = remDuration;
    this.monthsToLive = monthsToLive;
    this.data = data;
  }
  
  public String getPatientId(){ return this.patientId;  }
  public boolean getRemission(){ return this.remission; }
  public double getRemDuration(){ return this.remDuration; }
  public double getMonthsToLive(){ return this.monthsToLive; }
  public HashMap<String, Double> getHashMap() { return this.data; }
  public String getInfection() { return this.infection; }
}

class Solution
{
  public static void main (String[] args) throws Exception
  {
    ArrayList<Patient> patientList = new ArrayList<Patient>();
    patientList = importPatientInfo("trainingData.txt");
    /*for(int i = 0; i<patientList.size(); i++)
     {
     //System.out.println(patientList.get(i).getPatientId());
     }*/
    ArrayList<Token> hi = new ArrayList<Token>();
    hi = correlatedKeys(patientList);
    /*for (Token t: hi)
     {
     System.out.println(t.getKey() + " " + t.getsd());
     }*/
    System.out.println(hi.size());
  }
  
   private static ArrayList<Patient> importPatientInfo(String patientFilePath)
  {
    ArrayList<Patient> patient = new ArrayList<Patient>();
    try
    {
      BufferedReader reader = new BufferedReader (new FileReader(patientFilePath));
      
      String currentLine = reader.readLine();
      currentLine = currentLine.toLowerCase();
      currentLine = reader.readLine();
      String [] parts = currentLine.split("\t"); //put elements in array seperated by tabs
      String [] keys = new String [266];//string of the keys to hash map
      int counter = 0;
      for (int i = 1; i < 266; i++) //go through first block to get all keys into keys array 
      {
        // System.out.println(parts[i]);
        keys[counter] = parts[i];
        counter++;
      }  
      currentLine = reader.readLine();
      
      while(currentLine!= null)
      {
        currentLine = currentLine.toLowerCase();
        parts = currentLine.split("\t");
        
        
        int [] nonNumIndex = {1,4,5,6,7,8,9,10};
        
        // changes all nonnumerical attributes
        for (int m = 0; m < nonNumIndex.length; m++)
        {
          
          int i = nonNumIndex[m];
          
          if (parts[i].equals("no") || parts[i].equals("m") || parts[i].equals("neg") || parts[i].equals("anthra-hdac"))
          {
            parts[i] = "0";
          }
          
          if (parts[i].equals("yes") || parts[i].equals("f") || parts[i].equals("pos") || parts[i].equals("anthra-plus"))
          {
            parts[i] = "1";
          }
          
          if (parts[i].equals("na") || parts[i].equals("notdone") || parts[i].equals("nd"))
          {
            parts[i] = "8008135"; //arbitrary number to avoid NullPointer error - will be dealt with later
          }
        }
        
        HashMap<String, Double> aData = new HashMap<String, Double>();
        String aID;
        boolean aRemission;
        double aDuration;
        double aMonths;
        String infection = parts[11];
        aID = parts[0];
        if(parts[266].equals("complete_remission"))
        {
          aRemission = true;
        }
        else
        {
          aRemission = false;
        }
        if (!(parts[267].equals("na")))
        {
          aDuration =Double.parseDouble(parts[267]);
        }
        else{ aDuration = 0; }
        
        aMonths = Double.parseDouble(parts[268]);
        int counter2 = 0;
        for (int i=1; i<266; i++)
        {
          if (i == 11)
          {
            i++;
          }
          // System.out.println(parts[i]);
          else if(parts[i].equals("na"))
          {
            Double value = Double.parseDouble("8008135"); //arbitrary number to avoid NullPointer error - will be dealt with later
          }
          else
          {
            Double value = Double.parseDouble(parts[i]);
            
//          System.out.println(value);
            aData.put(keys[counter2], value);
          }
        }
        
        Patient newPatient = new Patient (aID, infection, aRemission, aDuration, aMonths, aData); //create a new patient object for each line of code
        patient.add(newPatient);
        currentLine = reader.readLine();
        
      }
      reader.close();
    }
    catch (IOException x)
    {
      System.out.println(x);
    }
    //returns array list
    return patient;
  }
  
  public static ArrayList<Patient> getRemissionPatients(ArrayList<Patient> patients)
  {
    ArrayList<Patient> remPatients = new ArrayList<Patient>();
    
    for (int i = 0; i < patients.size(); i++)
    {
      if(patients.get(i).getRemission())
      {
        remPatients.add(patients.get(i));
      }
    }
    return remPatients;
    
  }
  
  public static ArrayList<Patient> getResistantPatients(ArrayList<Patient> patients)
  {
    ArrayList<Patient> resPatients = new ArrayList<Patient>();
    
    for (int i = 0; i < patients.size(); i++)
    {
      if(!(patients.get(i).getRemission()))
      {
        resPatients.add(patients.get(i));
      }
    }
    return resPatients;
  }
  public static boolean predictRemStatus(HashMap<String, Double> unknownStatus, ArrayList<Token> rem, ArrayList<Token> res)
  {
    int trueCounter = 0;
    int falseCounter = 0;
    
    //Create ArrayList of relevant keys, HashMaps mapping relevant keys to their mean/SD values
    ArrayList<String> relRemKeys = new ArrayList<String>();
    HashMap<String,Double[]> relRem = new HashMap<String,Double[]>();
    
    for (int i = 0; i < rem.size(); i++)
    {
      relRemKeys.add(rem.get(i).getKey());
      Double [] meanSD = {(rem.get(i).getMean()), (rem.get(i).getsd())};
      relRem.put(rem.get(i).getKey(), meanSD);
    }
    
    ArrayList<String> relResKeys = new ArrayList<String>();
    HashMap<String,Double[]> relRes = new HashMap<String,Double[]>();
    
    for (int i = 0; i < res.size(); i++)
    {
      relResKeys.add(res.get(i).getKey());
      Double [] meanSD = {(res.get(i).getMean()), (res.get(i).getsd())};
      relRes.put(res.get(i).getKey(), meanSD);
    }
    
    // Loop through relevant keys in remission and resistant cases.
    // Checks if unknown value for a relevant key is in range (mean ± standard deviation)
    // Adds to relevant counter depending on whether key is or isn't in range
    
    for (int i = 0; i < relRem.size(); i++)
    {
      if (((unknownStatus.get(relRemKeys.get(i))) <= (relRem.get(relRemKeys.get(i))[0] + relRem.get(relRemKeys.get(i))[1])) || 
          ((unknownStatus.get(relRemKeys.get(i))) >= (relRem.get(relRemKeys.get(i))[0] - relRem.get(relRemKeys.get(i))[1])))
      {trueCounter++;}
      else
      {falseCounter++;}
    }
    
    for (int i = 0; i < relRes.size(); i++)
    {
      if (((unknownStatus.get(relResKeys.get(i))) <= (relRes.get(relResKeys.get(i))[0] + relRes.get(relResKeys.get(i))[1])) || 
          ((unknownStatus.get(relResKeys.get(i))) >= (relRes.get(relResKeys.get(i))[0] - relRes.get(relResKeys.get(i))[1])))
      { falseCounter++; }
      else
      { trueCounter++; }
    }
    
    // returns true if there are more remission indicators than resistance indicators, false otherwise
    if (trueCounter > falseCounter)
    { return true; }
    return false;
  }
  public static ArrayList<Token> correlatedKeys(ArrayList<Patient> patients) //returns list from least to greatest correlation to REM
  {
    ArrayList<Token> stdDev = new ArrayList<Token>();
    ArrayList<String> keySet = new ArrayList<String>();
    
    System.out.println(patients.get(0).getHashMap().size());
    Set<String> keys = patients.get(0).getHashMap().keySet();
    keySet.addAll(keys);
    System.out.println(keySet);
    for(int j = 0; j<266; j++)
    {
      int counter = 0;
      double arr[] = new double [patients.size()];
      for(int i =0; i<patients.size(); i++)
      {
        arr[i] = patients.get(i).getHashMap().get(keySet.get(counter));
        counter ++;
      }
      Token t = new Token(StatCalcs.standardDeviation(arr), keySet.get(counter));
      stdDev.add(t);
    }
    ArrayList<Token> result = new ArrayList<Token>();
    result = mergeSort(result);
    return result;
  }
  public static ArrayList<Token> mergeSort(ArrayList<Token> tok)
  {
    int middle = tok.size()/2;
    ArrayList<Token> left = new ArrayList<Token>();
    ArrayList<Token> right = new ArrayList<Token>(); 
    if(tok.size() <= 1)
    {
      return tok;
    }
    for(int i = 0; i<middle; i++)
    {
      left.add(i, tok.get(i));
    }
    for(int j = middle; j<=tok.size(); j++)
    {
      right.add(j, tok.get(j));
    }
    left = mergeSort(left);
    right = mergeSort(right);
    ArrayList<Token> result = merge(left, right);
    return result;
  }
  public static ArrayList<Token> merge(ArrayList<Token> left, ArrayList<Token> right)
  {
    int length = left.size() + right.size();
    ArrayList<Token> newArrayList = new ArrayList<Token>();
    
    int a = 0;
    int b = 0;
    for(int i = 0; i<newArrayList.size(); i++)
    {
      double d = left.get(a).getsd();
      double d2 = right.get(b).getsd();
      if((b>=right.size() || a<left.size() && d<d2))
      {
        newArrayList.add(i, left.get(a));
        a++;
      }
      else
      {
        newArrayList.add(i, right.get(b));
        b++;
      }
    }
    return newArrayList;
  }
  
}

class StatCalcs
{
  public static double calculateMean(double[] array){
    double sum = 0;
    for(int i=0; i<array.length; i++){
      sum += array[i];
    }
    double average = sum/array.length;
    return average;
  }
  
  public static double calculateMedian(double[] array){
    Arrays.sort(array);
    double median=0;
    if(array.length%2 == 0){
      median = ((double)array[array.length/2] + (double)array[array.length/2-1]);
    }
    else{
      median = (double)array[array.length/2];
    }
    return median;
  }
  
  public static double standardDeviation(double[] array){
    double average = calculateMean(array);
    double variance = 0;
    for(int i=0; i<array.length; i++){
      variance += ((array[i] - average)*(array[i] - average))/array.length;
    }
    double sD = Math.sqrt(variance);
    return sD;
  }
}
class Token
{
  private String key;
  private double sd;
  private double mean; 
  
  public Token  (double aSd, String aKey)
  {
    this.sd = aSd;
    this.key = key;
  }
  public String getKey()
  {
    return this.key;
  }
  public double getsd()
  {
    return this.sd;
  }
  public void setMean(double m)
  {
    this.mean = m;
  }
  
  public double getMean()
  {
    return this.mean;
  }
}         
