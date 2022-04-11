***********************MainActivity.java********************
package com.example.marksrecordv2;

import androidx.annotation.Nullable;
import androidx.appcompat.app.AppCompatActivity;
import androidx.recyclerview.widget.LinearLayoutManager;
import androidx.recyclerview.widget.RecyclerView;

import android.os.Bundle;
import android.view.MenuItem;
import android.view.View;
import android.widget.PopupMenu;
import android.widget.Toast;

import com.example.marksrecordv2.Utils.MyAppUtils;

import java.util.ArrayList;

public class MainActivity extends AppCompatActivity implements MyAppUtils.onClickListenerOpcijePredmeta {


    private Integer position;
    private String[] predmet;
    private RecyclerView recycler;
    private RecyclerViewAdapter adapter;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        recycler = findViewById(R.id.recycler);
        adapter = new RecyclerViewAdapter(this);
        recycler.setAdapter(adapter);
        recycler.setLayoutManager(new LinearLayoutManager(this, LinearLayoutManager.VERTICAL, false));

    }

    @Override
    public void onClickOpcijePredmeta(View view) {
        //Toast.makeText(this, "Hello", Toast.LENGTH_SHORT).show();

        // x = Toast.makeText(this, "Hello", Toast.LENGTH_SHORT);
        // x.show();
        position = (Integer) view.getTag();

        ArrayList<String[]> predmeti = MyAppUtils.getMockData();
        predmet = predmeti.get(position.intValue());
        PopupMenu popup = new PopupMenu(this, view);
        popup.getMenuInflater().inflate(R.menu.menu_card, popup.getMenu());
        popup.setOnMenuItemClickListener(new PopupMenu.OnMenuItemClickListener() {
            @Override
            public boolean onMenuItemClick(MenuItem menuItem) {
                Bundle args = new Bundle();
                switch (menuItem.getItemId()) {
                    case R.id.dodaj_ocenu: {
                        args.putString(PredmetDijalog.AKCIJA_DIJALOG, PredmetDijalog.DODAJ_OCENU);
                        PredmetDijalog dijalog = new PredmetDijalog(MainActivity.this);
                        dijalog.setArguments(args);
                        dijalog.show(getSupportFragmentManager(), PredmetDijalog.DODAJ_OCENU);
                        return true;

                    }

                    case R.id.obrisi_ocenu: {
                        args.putString(PredmetDijalog.AKCIJA_DIJALOG, PredmetDijalog.OBRISI_OCENU);
                        args.putString(PredmetDijalog.OBRISI_OCENU, predmet[1]);
                        PredmetDijalog dijalog = new PredmetDijalog(MainActivity.this);
                        dijalog.setArguments(args);
                        dijalog.show(getSupportFragmentManager(), PredmetDijalog.OBRISI_OCENU);
                        return true;

                    }
                    case R.id.obrisi_predmet: {
                        args.putString(PredmetDijalog.AKCIJA_DIJALOG, PredmetDijalog.OBRISI_PREDMET);
                        args.putString(PredmetDijalog.OBRISI_PREDMET, predmet[0]);
                        PredmetDijalog dijalog = new PredmetDijalog(MainActivity.this);
                        dijalog.setArguments(args);
                        dijalog.show(getSupportFragmentManager(), PredmetDijalog.OBRISI_PREDMET);
                        return true;
                    }
                }

                return false;
            }
        });
        popup.show();
    }
    @Override
    public void onSelectedItemOpcijePredmeta(@Nullable View view, Bundle args) {
        String akcija = args.getString(PredmetDijalog.AKCIJA_DIJALOG);
        switch (akcija) {
            case PredmetDijalog.DODAJ_OCENU: {
                int ocena = args.getInt(PredmetDijalog.DODAJ_OCENU);
                Toast.makeText(this, akcija + " " + ocena, Toast.LENGTH_SHORT).show();
                break;
            }

           case PredmetDijalog.OBRISI_OCENU:{
                int index=args.getInt(PredmetDijalog.OBRISI_OCENU);
                Toast.makeText(this, akcija+" "+index, Toast.LENGTH_SHORT).show();
                break;
            }

            case PredmetDijalog.OBRISI_PREDMET:{

                Toast.makeText(this, akcija+" "+predmet[0], Toast.LENGTH_SHORT).show();
                break;
            }
        }

        }
    }

**************PredmetDijalog.java***************
package com.example.marksrecordv2;

import android.app.AlertDialog;
import android.app.Dialog;
import android.content.DialogInterface;
import android.os.Bundle;
import android.widget.RadioButton;
import android.widget.RadioGroup;

import androidx.annotation.NonNull;
import androidx.annotation.Nullable;
import androidx.fragment.app.DialogFragment;

import com.example.marksrecordv2.Utils.MyAppUtils;

public class PredmetDijalog extends DialogFragment {
    public static final String AKCIJA_DIJALOG="Akcija Dijalog";
    public static final String DODAJ_OCENU="Dodaj ocenu";
    public static final String OBRISI_OCENU="Obrisi ocenu";
    public static final String OBRISI_PREDMET="Obrisi predmet";
    public static final String DODAJ_PREDMET="Dodaj predmet";


    public PredmetDijalog(MyAppUtils.onClickListenerOpcijePredmeta listenerOpcijePredmeta) {
        this.listenerOpcijePredmeta = listenerOpcijePredmeta;
    }

    private MyAppUtils.onClickListenerOpcijePredmeta listenerOpcijePredmeta;

    @NonNull
    @Override
    public Dialog onCreateDialog(@Nullable Bundle savedInstanceState) {
        Bundle args=getArguments();
        String akcija= args.getString(PredmetDijalog.AKCIJA_DIJALOG);
        AlertDialog.Builder builder= new AlertDialog.Builder(getContext());
        Dialog dijalog;
        switch (akcija){
            case DODAJ_OCENU:{
                builder.setTitle("Odaberi ocenu");
                builder.setView(R.layout.dialog_dodaj_ocenu);
                builder.setPositiveButton("Potvrdi", new DialogInterface.OnClickListener() {
                    @Override
                    public void onClick(DialogInterface dialogInterface, int i) {
                        Dialog dialog= (Dialog) dialogInterface;
                        RadioGroup radioGroup=dialog.findViewById(R.id.radioGroupDialog);
                        RadioButton radioButton=dialog.findViewById(radioGroup.getCheckedRadioButtonId());
                        int ocena= Integer.parseInt(radioButton.getText().toString());
                        Bundle args=new Bundle();
                        args.putInt(PredmetDijalog.DODAJ_OCENU,ocena);
                        args.putString(PredmetDijalog.AKCIJA_DIJALOG, PredmetDijalog.DODAJ_OCENU);
                        listenerOpcijePredmeta.onSelectedItemOpcijePredmeta(null,args);
                    }
                });
                dijalog=builder.create();
                return dijalog;
            }
            case OBRISI_OCENU:{
                builder.setTitle("Odaberi ocenu");
                int[] ocene_int=MyAppUtils.getArrayOcena(args.getString(OBRISI_OCENU));
                CharSequence[] ocene= MyAppUtils.listaOcena(ocene_int);
                builder.setItems(ocene, new DialogInterface.OnClickListener() {
                    @Override
                    public void onClick(DialogInterface dialogInterface, int i) {
                        Bundle args = new Bundle();
                        args.putInt(OBRISI_OCENU, i);
                        args.putString(AKCIJA_DIJALOG, OBRISI_OCENU);
                        listenerOpcijePredmeta.onSelectedItemOpcijePredmeta(null, args);
                    }
                });
                builder.setNegativeButton("Ponisti", new DialogInterface.OnClickListener() {
                    @Override
                    public void onClick(DialogInterface dialogInterface, int i) {
                        dialogInterface.dismiss();
                    }
                });
                dijalog=builder.create();
                return dijalog;
                }


            case OBRISI_PREDMET:{
                builder.setTitle("Brisanje predmeta");

                String predmet=getArguments().getString(OBRISI_PREDMET);
                builder.setMessage("Potvrdite da bi ste obrisali "+ predmet);
                builder.setPositiveButton("Potvrdi", new DialogInterface.OnClickListener() {
                    @Override
                    public void onClick(DialogInterface dialogInterface, int i) {
                        Bundle args = new Bundle();
                        args.putString(AKCIJA_DIJALOG, OBRISI_PREDMET);
                        listenerOpcijePredmeta.onSelectedItemOpcijePredmeta(null, args);
                    }
                });
                builder.setNegativeButton("Odustani", new DialogInterface.OnClickListener() {
                    @Override
                    public void onClick(DialogInterface dialogInterface, int i) {
                        dialogInterface.dismiss();
                    }
                });
                dijalog=builder.create();
                return dijalog;
            }


        }

        return super.onCreateDialog(savedInstanceState);
    }
}

***************************RecyclerViewAdapter.java*****************************
package com.example.marksrecordv2;

import android.content.Context;
import android.view.LayoutInflater;
import android.view.View;
import android.view.ViewGroup;
import android.widget.ImageButton;
import android.widget.TextView;

import androidx.annotation.NonNull;
import androidx.recyclerview.widget.RecyclerView;

import com.example.marksrecordv2.Utils.MyAppUtils;

import java.util.ArrayList;

public class RecyclerViewAdapter extends RecyclerView.Adapter<RecyclerViewAdapter.ViewHolder>{

    private Context kontekst;
    private ArrayList<String[]> dataPredmet;

    public RecyclerViewAdapter(Context kontekst) {
        this.kontekst = kontekst;
        dataPredmet = MyAppUtils.getMockData();
    }

    @NonNull
    @Override
    public ViewHolder onCreateViewHolder(@NonNull ViewGroup parent, int viewType) {
        LayoutInflater inflater = LayoutInflater.from(kontekst);
        View viewCard = inflater.inflate(R.layout.card_layout, parent, false);
        ViewHolder holder = new ViewHolder(viewCard);
        return holder;
    }

    @Override
    public void onBindViewHolder(@NonNull ViewHolder holder, int position) {
        String ocene = dataPredmet.get(position)[1];
        String prosek = MyAppUtils.prosekOcena(MyAppUtils.getArrayOcena(ocene));
        holder.nazivPredmeta.setText(dataPredmet.get(position)[0]);
        holder.ocenePredmeta.setText(ocene);
        holder.prosekPredmeta.setText(prosek);
        holder.dugmePredmeta.setTag(position);
        holder.dugmePredmeta.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {
                MyAppUtils.onClickListenerOpcijePredmeta listener = (MyAppUtils.onClickListenerOpcijePredmeta) kontekst;
                listener.onClickOpcijePredmeta(view);

            }
        });

    }

    @Override
    public int getItemCount() {
        return dataPredmet.size();
    }

    public class ViewHolder extends RecyclerView.ViewHolder{

        TextView nazivPredmeta;
        TextView ocenePredmeta;
        TextView prosekPredmeta;
        ImageButton dugmePredmeta;

        public ViewHolder(@NonNull View itemView) {
            super(itemView);
            nazivPredmeta = itemView.findViewById(R.id.nazivCard);
            ocenePredmeta = itemView.findViewById(R.id.oceneCard);
            prosekPredmeta = itemView.findViewById(R.id.prosekCard);
            dugmePredmeta = itemView.findViewById(R.id.opcijeCard);
        }
    }
}

************************************menu_card.xml********************

<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">

    <item android:id="@+id/dodaj_ocenu"
        android:title="Dodaj ocenu"/>

    <item android:id="@+id/obrisi_ocenu"
        android:title="Obrisi ocenu"/>

    <item android:id="@+id/obrisi_predmet"
        android:title="Obrisi predmet"/>
</menu>

******************************MyAppUtils.java**********************************

package com.example.marksrecordv2.Utils;

import android.content.Context;
import android.os.Bundle;
import android.view.View;

import androidx.annotation.NonNull;
import androidx.annotation.Nullable;


import java.text.DecimalFormat;
import java.util.ArrayList;

public interface MyAppUtils {


   // public interface onClickListenerOpcijePredmeta{

     //   void onClickOpcijePredmeta(View view);
      //  default void onSelectedItemOpcijePredmeta(@Nullable View view, Bundle args) { };

    //}

    public interface onClickListenerOpcijePredmeta{

        void onClickOpcijePredmeta(View view);
        default void onSelectedItemOpcijePredmeta(@Nullable View view,Bundle args){ };

    }


    static void test(Context context){
    }

    static ArrayList<String[]> getMockData(){
        ArrayList izlaz = new ArrayList();
        izlaz.add(new String[]{"Likovno"," 2 2 5"});
        izlaz.add(new String[]{"Fizicko"," 2 3 3 5"});
        return izlaz;
    }

    //STRING  " 5 4 5" -> INT [ 5, 4, 5]
    @NonNull
    static int[] getArrayOcena(@NonNull String ocena_str){
        final int CODE_0 = 48;
        final int CODE_6 = 54;
        int[] izlaz = new int[20];
        int j=0;
        for(int i=0; i<ocena_str.length();i++){
            int code = ocena_str.charAt(i);
            if(code > CODE_0 && code < CODE_6) izlaz[j++]= code - CODE_0;
        }
        return izlaz;
    }

    //INT [ 1, 2 5 ] -> "STRING 1 2 5"
    @NonNull
    static String getStringArray(@NonNull int[] ocene_int){
        StringBuilder izlaz = new StringBuilder();
        for(int o:ocene_int){
            if(o==0) return izlaz.toString();
            izlaz.append(" ").append(o); //  o = 1 -> izlaz = izlaz + " 0"
        }
        return izlaz.toString();
    }

    @NonNull
    static int findFirstIndexOfIntArray(int[] array, int element){
        for(int i=0; i<array.length;i++){
            if(array[i] == element) return i;
        }
        return -1;
    }

    @NonNull
    static String prosekOcena(int[] ocene){
        double izlaz = 0.0d;
        for(int i = 0; i<ocene.length;i++){
            if(ocene[i] == 0) {
                return new DecimalFormat("#.##").format(izlaz/i);
            }
            izlaz += ocene[i];
        }
        return new DecimalFormat("#.##").format(izlaz/ocene.length);
    }

    static CharSequence[] listaOcena(int[] ocena_int){
        int length = MyAppUtils.findFirstIndexOfIntArray(ocena_int,0);
        CharSequence[] izlaz = new CharSequence[length];
        for(int i =0; i<length;i++){
            izlaz[i]= String.valueOf(ocena_int[i]);
        }
        return izlaz;
    }

}


**********************************card_layout.xml**************************

<?xml version="1.0" encoding="utf-8"?>
<androidx.cardview.widget.CardView xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:theme="@style/MyCard.MyText"
    android:id="@+id/card">

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="horizontal">

        <TextView
            android:layout_width="150dp"
            android:layout_height="wrap_content"
            android:id="@+id/nazivCard"
            android:text="Naziv"
            android:layout_marginHorizontal="3dp"/>
        <TextView
            android:id="@+id/oceneCard"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="1"
            android:text="Ocene"
            android:layout_marginHorizontal="3dp"/>
        <TextView
            android:id="@+id/prosekCard"
            android:layout_width="50dp"
            android:layout_height="wrap_content"
            android:text="xxxx"
            android:textAlignment="center"
            android:layout_marginHorizontal="5dp"
            android:paddingStart="0dp"/>

        <ImageButton
            android:id="@+id/opcijeCard"
            android:layout_width="55dp"
            android:layout_height="55dp"
            android:layout_marginHorizontal="3dp"
            android:src="@drawable/ic_baseline_more_vert_24" />

    </LinearLayout>

</androidx.cardview.widget.CardView>

**********************************dialog_dodaj_ocenu.xml**************************

<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:maxHeight="300dp">

    <RadioGroup
        android:id="@+id/radioGroupDialog"
        android:orientation="horizontal"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent">
        <RadioButton
            android:id="@+id/ocene1"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:checked="true"
            android:text="1"/>
        <RadioButton
            android:id="@+id/ocene2"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:text="2"/>
        <RadioButton
            android:id="@+id/ocene3"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:text="3"/>
        <RadioButton
            android:id="@+id/ocene4"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:text="4"/>

            <RadioButton
                android:id="@+id/ocene5"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:text="5"/>

    </RadioGroup>

</androidx.constraintlayout.widget.ConstraintLayout>



**********************************activity_main.xml**************************

<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <androidx.recyclerview.widget.RecyclerView
        android:layout_width="409dp"
        android:layout_height="600dp"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent"
        android:id="@+id/recycler"
        app:flow_verticalBias="0.21"/>
</androidx.constraintlayout.widget.ConstraintLayout>

