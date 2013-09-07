Mac_App
=======

Real Deal import java.util.ArrayList;
import android.app.Activity;
import android.content.ContentResolver;
import android.database.Cursor;
import android.os.Bundle;
import android.provider.ContactsContract;
import android.util.Log;
import android.view.View;
import android.view.View.OnClickListener;
import android.view.inputmethod.InputMethodManager;
import android.widget.AdapterView;
import android.widget.ArrayAdapter;
import android.widget.AutoCompleteTextView;
import android.widget.Button;
import android.widget.EditText;
import android.widget.Toast;
import android.widget.AdapterView.OnItemClickListener;
import android.widget.AdapterView.OnItemSelectedListener;
 
public class AutocompleteMain extends Activity implements  OnItemClickListener, OnItemSelectedListener  {
  
    // Initialize variables
      
    AutoCompleteTextView textView=null;
    private ArrayAdapter<String> adapter;
     
    // Store contacts values in these arraylist
    public static ArrayList<String> phoneValueArr = new ArrayList<String>();
    public static ArrayList<String> nameValueArr = new ArrayList<String>();
     
    EditText toNumber=null;
    String toNumberValue="";
     
     
    /** Called when the activity is first created. */
    @Override
    public void onCreate(Bundle bundle) {
        super.onCreate(bundle);
         
        setContentView(R.layout.autocomplete_main);
         
        final Button Send = (Button) findViewById(R.id.Send);
         
        // Read contact data and add data to ArrayAdapter
        // ArrayAdapter used by AutoCompleteTextView
             
           readContactData();
                 
             
       /********** Button Click pass textView object ***********/
        Send.setOnClickListener(BtnAction(textView));
         
         
    }
 
    private OnClickListener BtnAction(final AutoCompleteTextView toNumber) {
        return new OnClickListener() {
             
            public void onClick(View v) {
                 
                String NameSel = "";
                NameSel = toNumber.getText().toString();
                 
                 
                final String ToNumber = toNumberValue;
                 
                 
                if (ToNumber.length() == 0 ) {
                    Toast.makeText(getBaseContext(), "Please fill phone number",
                            Toast.LENGTH_SHORT).show();
                }
                else
                {
                    Toast.makeText(getBaseContext(), NameSel+" : "+toNumberValue,
                                Toast.LENGTH_LONG).show();
                }
                 
            }
        };
    }   
     
     
    // Read phone contact name and phone numbers 
     
    private void readContactData() {
         
        try {
             
            /*********** Reading Contacts Name And Number **********/
             
            String phoneNumber = "";
            ContentResolver cr = getBaseContext()
                    .getContentResolver();
             
            //Query to get contact name
             
            Cursor cur = cr
                    .query(ContactsContract.Contacts.CONTENT_URI,
                            null,
                            null,
                            null,
                            null);
             
            // If data data found in contacts 
            if (cur.getCount() > 0) {
                 
                Log.i("AutocompleteContacts", "Reading   contacts........");
                 
                int k=0;
                String name = "";
                 
                while (cur.moveToNext()) 
                {
                     
                    String id = cur
                            .getString(cur
                                    .getColumnIndex(ContactsContract.Contacts._ID));
                    name = cur
                            .getString(cur
                                    .getColumnIndex(ContactsContract.Contacts.DISPLAY_NAME));
                     
                    //Check contact have phone number
                    if (Integer
                            .parseInt(cur
                                    .getString(cur
                                        .getColumnIndex(ContactsContract.Contacts.HAS_PHONE_NUMBER))) > 0) 
                    {
                
                            int j=0;
                             
                            while (pCur
                                    .moveToNext()) 
                            {
                                // Sometimes get multiple data 
                                if(j==0)
                                {
                                    // Get Phone number
                                    phoneNumber =""+pCur.getString(pCur
                                                .getColumnIndex(ContactsContract.CommonDataKinds.Phone.NUMBER));
                                     
                                    // Add contacts names to adapter
                                    adapter.add(name);
                                     
                                    // Add ArrayList names to adapter
                                    phoneValueArr.add(phoneNumber.toString());
                                    nameValueArr.add(name.toString());
                                     
                                    j++;
                                    k++;
                                }
                            }  // End while loop
                            pCur.close();
                        } // End if
                     
                }  // End while loop
 
            } // End Cursor value check
            cur.close();
                     
          
        } catch (Exception e) {
             Log.i("AutocompleteContacts","Exception : "+ e);
        }
                     
     
   }
 
    @Override
    public void onItemSelected(AdapterView<?> arg0, View arg1, int position,
            long arg3) {
        // TODO Auto-generated method stub
        //Log.d("AutocompleteContacts", "onItemSelected() position " + position);
    }
 
    @Override
    public void onNothingSelected(AdapterView<?> arg0) {
        // TODO Auto-generated method stub
         
        InputMethodManager imm = (InputMethodManager) getSystemService(
                INPUT_METHOD_SERVICE);
            imm.hideSoftInputFromWindow(getCurrentFocus().getWindowToken(), 0);
 
    }
 
    @Override
    public void onItemClick(AdapterView<?> arg0, View arg1, int arg2, long arg3) {
        // TODO Auto-generated method stub
         
            // Get Array index value for selected name
             int i = nameValueArr.indexOf(""+arg0.getItemAtPosition(arg2));
            
            // If name exist in name ArrayList
            if (i >= 0) {
                 
                // Get Phone Number
                toNumberValue = phoneValueArr.get(i);
                 
                InputMethodManager imm = (InputMethodManager) getSystemService(
                        INPUT_METHOD_SERVICE);
                    imm.hideSoftInputFromWindow(getCurrentFocus().getWindowToken(), 0);
 
                // Show Alert       
                Toast.makeText(getBaseContext(), 
                            "Position:"+arg2+" Name:"+arg0.getItemAtPosition(arg2)+" Number:"+toNumberValue,
                            Toast.LENGTH_LONG).show();
                 
                Log.d("AutocompleteContacts", 
                            "Position:"+arg2+" Name:"+arg0.getItemAtPosition(arg2)+" Number:"+toNumberValue);
                 
            }
         
    }
     
    protected void onResume() {
        super.onResume();
    }
  
    protected void onDestroy() {
        super.onDestroy();
    }
     
}
