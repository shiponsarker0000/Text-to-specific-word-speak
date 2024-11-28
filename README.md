TextView এর মধ্যে যেই টেক্সটি দেখানো হইছে। সেই টেক্স এর একটি শব্দ সিলেক্ট করলে তার উচ্চারন বলে শুনাবে।
MainActivity (XML) কোডিং --------------------------

<?xml version="1.0" encoding="utf-8"?>
<LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/main"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity"
    android:orientation="vertical"
    >

    <TextView
        android:id="@+id/tvText"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text=" বাংলাদেশ Lorem ipsum is simply dummy text of the printing and typesetting industry. Lorem Ipsum has been the industry's standard dummy text ever since the 1500s."
        android:textSize="18sp"
        android:textColor="#000"
        android:background="#EEEEEE"
        android:padding="8dp"
        android:textIsSelectable="true"
        />

</LinearLayout>
//-------------------------------------------------
//----------------------- Java code ----------------


import android.os.Bundle;
import android.speech.tts.TextToSpeech;
import android.util.Log;
import android.view.MotionEvent;
import android.widget.TextView;
import android.widget.Toast;

import androidx.activity.EdgeToEdge;
import androidx.appcompat.app.AppCompatActivity;
import androidx.core.graphics.Insets;
import androidx.core.view.ViewCompat;
import androidx.core.view.WindowInsetsCompat;

import java.util.Locale;

public class MainActivity extends AppCompatActivity {

    private TextToSpeech textToSpeech;
    private TextView tvText;


    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        // TextView Initialize
        tvText = findViewById(R.id.tvText);

        // Text-to-Speech Initialize
        textToSpeech = new TextToSpeech(this, status -> {
            if (status == TextToSpeech.SUCCESS) {
                int langResult = textToSpeech.setLanguage(Locale.ENGLISH);
                if (langResult == TextToSpeech.LANG_MISSING_DATA ||
                        langResult == TextToSpeech.LANG_NOT_SUPPORTED) {
                    Log.e("TTS", "Language not supported or missing data.");
                }
            } else {
                Log.e("TTS", "Initialization failed.");
            }
        });

        // Set OnTouchListener for TextView
        tvText.setOnTouchListener((v, event) -> {
            if (event.getAction() == MotionEvent.ACTION_UP) {
                int start = tvText.getSelectionStart();
                int end = tvText.getSelectionEnd();
                if (start != -1 && end != -1 && start != end) {
                    speakSelectedText();
                }
            }
            return false;
        });
    }

    private void speakSelectedText() {
        int start = tvText.getSelectionStart();
        int end = tvText.getSelectionEnd();

        if (start != -1 && end != -1 && start != end) {
            String selectedText = tvText.getText().toString().substring(start, end);
            if (!selectedText.isEmpty()) {
                if (textToSpeech != null && textToSpeech.isLanguageAvailable(Locale.ENGLISH) == TextToSpeech.LANG_AVAILABLE) {
                    textToSpeech.speak(selectedText, TextToSpeech.QUEUE_FLUSH, null, null);
                } else {
                    Log.e("TTS", "TextToSpeech is not initialized or language is not available.");
                }
            }
        } else {
            Toast.makeText(this, "No valid selection made.", Toast.LENGTH_SHORT).show();
        }
    }

    @Override
    protected void onDestroy() {
        super.onDestroy();
        if (textToSpeech != null) {
            textToSpeech.stop();
            textToSpeech.shutdown();
        }
    }


}//--------------------------- public Class end method -------------------------------





