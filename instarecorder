import streamlit as st
from openai import OpenAI

# --- Setup ---
st.set_page_config(page_title="Lecture Scribe AI", page_icon="🎙️")
st.title("🎙️ Lecture Scribe & Study-Gen")
st.write("Record your lecture. Get text, notes, quizzes, and flashcards instantly.")

client = OpenAI(api_key="YOUR_OPENAI_API_KEY")

# --- Recording & Upload ---
# audio_input allows live recording or file upload in one widget
audio_data = st.audio_input("Record your lecture here")

if audio_data:
    # Display the audio player so you can review the recording
    st.audio(audio_data)
    
    if st.button("✨ Generate Study Suite"):
        with st.spinner("Step 1: Converting Audio to Text..."):
            try:
                # 1. AUDIO TO TEXT (Whisper)
                transcript = client.audio.transcriptions.create(
                    model="whisper-1", 
                    file=audio_data,
                    response_format="text"
                )
                
                with st.spinner("Step 2: Creating Study Materials..."):
                    # 2. TEXT TO NOTES/QUIZ/CARDS (GPT-4o)
                    response = client.chat.completions.create(
                        model="gpt-4o",
                        messages=[
                            {"role": "system", "content": """
                                You are a top-tier academic assistant. Your goal is to turn transcripts into a full study suite.
                                - HIGHLIGHT: Use 💡 for critical exam points.
                                - CLEAR NOTES: Use markdown headers and bold key terms.
                                - QUIZ: Create 5 challenging Multiple Choice Questions.
                                - FLASHCARDS: Create 5 'Front: Back:' style flashcards.
                            """},
                            {"role": "user", "content": f"Transcript: {transcript}"}
                        ],
                        temperature=0.3
                    )
                    
                    full_analysis = response.choices[0].message.content

                # --- Organizing the UI ---
                st.success("Processing Complete!")
                
                # Create Tabs for a clean view
                tab_trans, tab_notes = st.tabs(["📝 Full Transcript", "📚 Study Suite"])
                
                with tab_trans:
                    st.text_area("Raw Text:", transcript, height=300)
                    st.download_button("Download Transcript (.txt)", transcript)

                with tab_notes:
                    st.markdown(full_analysis)
                    st.download_button("Download Study Suite (.md)", full_analysis)

            except Exception as e:
                st.error(f"Error: {e}")

else:
    st.info("Click the microphone above to start your lecture recording.")
