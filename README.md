package com.ko_dr_sol.fundquery;


import java.util.ArrayList;




import java.util.List;

import com.ko_dr_sol.SQLite.fund;
import com.ko_dr_sol.SQLite.fundSQLite;

import android.app.Activity;
import android.os.Bundle;
import android.view.View;
import android.widget.AdapterView;
import android.widget.AdapterView.OnItemSelectedListener;
import android.widget.ArrayAdapter;
import android.widget.Button;
import android.widget.EditText;
import android.widget.Spinner;
import android.widget.TextView;
import android.widget.Toast;


public class MainActivity extends Activity {
	private fundSQLite dbHlp; 
	private ArrayList<fund> funds; 
	private int index; 
	
	
	
	
	
	Spinner fundname;  //OK基金名稱
	TextView coupon;   //OK配息
	EditText first;    //第一區間NO--
	EditText list;     //最後區間NO--
	Button interval;   //區間查詢NO--
	Button inquiry;   //查詢我的最愛NO
	Button join;      //加入我的最愛NO
	Button delete;    //刪除我的最愛NO
	
	Methods m=new Methods();
	@Override
	protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.activity_main);
		sours();//View連結
		
		m.connection();//jsoup	
		m.connection2();//jsoup	
		m.couponscreening();
				//把陣列設給Spinner
				ArrayAdapter<String> adapter=new ArrayAdapter<String>(MainActivity.this,android.R.layout.simple_list_item_1,m.fundname);				
				//// set up
				fundname.setAdapter(adapter);
				//偵聽Spinner每個Item被案下動作
				fundname.setOnItemSelectedListener(new OnItemSelectedListener() 
				{		
					@Override
					public void onItemSelected(AdapterView<?> parent,
							View view, int position, long id) {
						//coupon.setText(m.fundCoupon[position].toString()/*+m.fundCouponCycle.get(position).toString()*/);
						coupon.setText(m.fundCoupon.get(position).toString()/*+m.fundCouponCycle.get(position).toString()*/);
						//coupon.setText(m.fundCoupon[position].toString()+m.fundCouponCycle[position].toString());		
						coupon.invalidate();
						}
					@Override
					public void onNothingSelected(AdapterView<?> parent) {
					}
				});	
	}
	
/////////////////////////////////////////////////////////////SQLite	++++++++++++++++++++++++++++++++++++
	@Override
	public void onResume() {
		super.onResume();
		if(dbHlp == null)
			dbHlp = new fundSQLite(this); 
		funds = dbHlp.getAllSites();
		showfunds(index);		
	}

	@Override
	public void onPause() {
		super.onPause();
		if(dbHlp != null){
			dbHlp.close(); 
			dbHlp = null;
		}
		funds.clear(); 
	}
	
	private void showfunds(int index) {	
//		if(funds.size() > 0){
//			tvRow.setText((index + 1) + "/" + funds.size() + 
//							getString(R.string.rowid_rowCount));
//			tvId.setText(funds.get(index).getId());
//			tvName.setText(funds.get(index).getName());
//			tvPhoneNo.setText(funds.get(index).getPhoneNo());
//			tvAddress.setText(funds.get(index).getAddress());	
//		}else{
//			tvRow.setText("0/0" + getString(R.string.noData));
//			tvId.setText("");
//			tvName.setText("");
//			tvPhoneNo.setText("");
//			tvAddress.setText("");				
//		}
	}
	
/////////////////////////////////////////////////////////////SQLite------------------------------------------------	
	
	
	//interval

	public void interval(View aa){  
		m.fundnameDownload=null;//區間查詢暗扭NO--這配息在重爪回圈
		m.fundnameDownload=new String[2000];//基金名字網頁下載資料
		//List<String> fundname=new ArrayList<>(500);//基金名字
		m.fundname=null;//基金名字
		m.fundname=new ArrayList<>(500);//基金名字
		//List<String> fundCouponDownload=new ArrayList<String>(500);//基金配息網頁下載資料
		m.fundCouponDownload=null;//基金配息網頁下載資料
		m.fundCouponDownload=new String[2000];//基金配息網頁下載資料
		//List<String> fundCoupon=new ArrayList<>(500) ;//基金配息
		m.fundCoupon=null ;//基金配息
		m.fundCoupon=new ArrayList<>(500) ;//基金配息
		
		m.CouponVariables=0;
		m.nameVariables=0;
		m.nameVariablesDownload=0;
		if(first.getText().toString().trim()==""){
			m.one=5.0;
		}
		if(first.getText().toString().trim()==null){
			m.one=5.0;
		}
		if(list.getText().toString().trim()==""){
			m.tow=25.0;
		}
		if(list.getText().toString().trim()==null){
			m.tow=25.0;
		}
		if(first.getText().toString().trim()!=""  &&  first.getText().toString().trim()!=null  
				&&  Double.parseDouble(first.getText().toString().trim())>0 
				&&  Double.parseDouble(first.getText().toString().trim())<50
				&&  Double.parseDouble(first.getText().toString().trim())<
					Double.parseDouble(list.getText().toString().trim())){
			m.tow=Double.parseDouble(list.getText().toString().trim());	
			m.one=Double.parseDouble(first.getText().toString().trim());
		}
		m.connection();//jsoup	
		m.connection2();//jsoup	
		m.couponscreening();
		//把陣列設給Spinner
		ArrayAdapter<String> adapter=new ArrayAdapter<String>(MainActivity.this,android.R.layout.simple_list_item_1,m.fundname);				
		//// set up
		fundname.setAdapter(adapter);
		//偵聽Spinner每個Item被案下動作
		fundname.setOnItemSelectedListener(new OnItemSelectedListener() 
		{		
			@Override
			public void onItemSelected(AdapterView<?> parent,
					View view, int position, long id) {
				//coupon.setText(m.fundCoupon[position].toString()/*+m.fundCouponCycle.get(position).toString()*/);
				coupon.setText(m.fundCoupon.get(position).toString()/*+m.fundCouponCycle.get(position).toString()*/);//coupon.setText(m.fundCoupon[position].toString()+m.fundCouponCycle[position].toString());		
				coupon.invalidate();
				}
			@Override
			public void onNothingSelected(AdapterView<?> parent) {
			}
		});	
		fundname.invalidate();
	}
	
	public void join(View aa){                   //加入我的最愛暗扭NO
		String id = null ;//= etId.getText().toString().trim();
		String name = null ;//= etName.getText().toString().trim();
		String coupon1 = coupon.getText().toString().trim();
		String javascript1 = null ;//= etAddress.getText().toString().trim();
		if(id.length() <= 0 || name.length() <= 0){
			Toast.makeText(MainActivity.this, getString(R.string.blank), 
							Toast.LENGTH_SHORT).show();
			return;
		}
		
		StringBuilder sb = new StringBuilder();
		fund fund = new fund(id, name, coupon1, javascript1); 
		long rowId = dbHlp.insertDB(fund);
		if(rowId != -1){
			sb.append(getString(R.string.insert_success));
		}else{
			sb.append(getString(R.string.insert_fail));
		}
		Toast.makeText(MainActivity.this, sb, Toast.LENGTH_SHORT).show();
	}

	public void delete(View aa){                 //刪除我的最愛暗扭NO
		
	}

	public void inquiry(View aa){                //查詢我的最愛暗扭NO
		
	}
	//View連結
	public void sours(){
		fundname = (Spinner) findViewById(R.id.spinner1);   //ok基金名稱
		coupon=(TextView) findViewById(R.id.coupon);        //ok基金配息
		first=(EditText) findViewById(R.id.fist);           //第一區間NO
		list=(EditText) findViewById(R.id.last);            //最後區間NO
		interval=(Button) findViewById(R.id.interval);      //區間查詢NO
		inquiry=(Button) findViewById(R.id.inquiry);        //查詢我的最愛NO
		join=(Button) findViewById(R.id.join);              //加入我的最愛NO
		delete=(Button) findViewById(R.id.delete);          //刪除我的最愛NO
	}
	
}





//@Override
//protected void onResume() {
//	super.onResume();
//	one=Double.parseDouble(first.getText().toString());
//	tow=Double.parseDouble(list.getText().toString());
//}

