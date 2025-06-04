import streamlit as st
import openai
import pdfplumber
from docx import Document

# Set your API key
openai.api_key = "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"

# ---- FUNCTIONS ----
def extract_text_from_pdf(uploaded_file):
    with pdfplumber.open(uploaded_file) as pdf:
        return "\n".join([page.extract_text() for page in pdf.pages if page.extract_text()])

def extract_text_from_docx(uploaded_file):
    doc = Document(uploaded_file)
    return "\n".join([para.text for para in doc.paragraphs if para.text.strip()])

def get_feedback_and_rewrite(resume_text, guide_text):
    system_prompt = f"""
    You are a legal career advisor at a U.S. law school. Your job is to help students improve their resumes
    to meet legal industry standards. Use the rules from the resume guide below:

    [BEGIN GUIDE]
    LEGAL RESUME WRITING GUIDELINES

OVERVIEW:
A legal resume is a concise, tailored marketing tool used to secure job interviews in the legal profession. It should be clear, honest, well-structured, and free of errors. Employers often review resumes quickly, so impact and clarity are essential.

PAGE LENGTH:
- Keep to one page if possible.
- Two pages are acceptable for significant, relevant experience (especially for public interest or graduate-level resumes).

STYLE & FORMATTING:
- Use standard black-and-white formatting.
- Fonts: Times New Roman, Helvetica, or other businesslike fonts.
- Font size: 10.5–12pt.
- Margins: 0.5" to 1".
- No graphics, colors, or photos.
- Proofread carefully for grammar, punctuation, and formatting.

DO NOT INCLUDE:
- Objective statements.
- Salary history.
- Personal political or religious affiliations (unless directly relevant to employment).
- Subjective language (e.g., “expertly led,” “outstanding skills”).
- References or “References available upon request.”

REQUIRED SECTIONS (IN THIS ORDER):

1. **HEADING**
- Include full name, professional email, phone number, and optional LinkedIn URL.
- You may include a current and/or permanent address.
- Show geographic ties where appropriate.

2. **EDUCATION**
- List degrees in reverse chronological order.
- Include: institution, location, degree (e.g., Juris Doctor), and expected/actual graduation date.
- GPA: only include if 3.0 or higher and reported exactly as issued by the registrar (no rounding).
- List honors (e.g., Dean’s List, CALI Awards) and activities under each institution.
- You may separate Law Review as its own section or under honors.

3. **EXPERIENCE**
- List in reverse chronological order.
- Include: job title, organization, location, and dates of employment.
- Describe work using legal or transferable action verbs (see action verb list).
- Focus on skills relevant to legal employers: research, writing, analysis, client communication, advocacy, etc.
- Use bullet points or short paragraphs, but ensure each line adds value.

4. **SKILLS & INTERESTS**
- Include languages, certifications, bar admissions, and technical/legal skills.
- Interests should be specific and unique (e.g., “marathon runner” or “Spanish literature enthusiast”).

ADDITIONAL NOTES:
- Do not list high school unless highly prestigious or directly relevant.
- Public interest resumes may exceed one page to demonstrate commitment.
- Tailor resume for each legal employer type: firms, clerkships, nonprofits, or government roles.

APPENDIX: APPROVED ACTION VERBS (sample categories)

*Research/Analysis:* Analyzed, Evaluated, Investigated, Measured, Surveyed  
*Communication:* Drafted, Presented, Authored, Reviewed, Edited  
*Project Leadership:* Coordinated, Organized, Executed, Managed  
*Team Management:* Mentored, Trained, Supervised, Facilitated  
*Client Support:* Advised, Educated, Informed, Resolved  
*Change/Impact:* Streamlined, Redesigned, Implemented, Restructured  
*Achievements:* Exceeded, Completed, Won, Delivered

All resume content should reflect professionalism, clarity, and suitability for a legal audience.

    [END GUIDE]

    First, review the user's resume section by section and provide feedback and improvements based on the guide.

    Then, generate a full revised version of the resume, properly formatted and rewritten in a way that best supports
    a legal career (private firm, public interest, clerkship, etc.). Use only accurate, professional, and concise language.
    """
    
    user_prompt = f"""Here is the student's resume to review and revise:

    [BEGIN RESUME]
    {resume_text}
    [END RESUME]
    """

    response = openai.ChatCompletion.create(
        model="gpt-4-1106-preview",
        messages=[
            {"role": "system", "content": system_prompt},
            {"role": "user", "content": user_prompt}
        ],
        temperature=0.5
    )
    return response['choices'][0]['message']['content']

# ---- STREAMLIT UI ----
st.set_page_config(page_title="Legal Resume Optimizer", layout="wide")
st.title("🧑‍⚖️ Legal Resume Optimizer")

st.markdown("Upload your resume (PDF or DOCX) and get expert feedback and a fully rewritten legal version.")

uploaded_resume = st.file_uploader("📄 Upload Resume", type=["pdf", "docx"])
guide_text = """[Paste or preload your cleaned guide text here, or load from file]"""

# You can paste your cleaned-up guide directly here to avoid uploading every time
# Or, load the PDF text from a file and cache it
if uploaded_resume:
    if uploaded_resume.name.endswith(".pdf"):
        resume_text = extract_text_from_pdf(uploaded_resume)
    else:
        resume_text = extract_text_from_docx(uploaded_resume)

    if st.button("✨ Improve My Resume"):
        with st.spinner("Processing with GPT-4 Turbo..."):
            result = get_feedback_and_rewrite(resume_text, guide_text)
        st.success("Done! Review your results below:")
        st.subheader("📌 Section-by-Section Feedback + Suggested Rewrite")
        st.text_area("Output", result, height=800)
