# 🔧 ASCIS — Setup Guide

## Prerequisites
- Python 3.10+
- pip
- Google Gemini API key

## Installation

```bash
# Clone the repository
git clone https://github.com/your-repo/smart_supply_chain.git
cd smart_supply_chain

# Create virtual environment
python -m venv venv
venv\Scripts\activate  # Windows
# source venv/bin/activate  # Linux/Mac

# Install dependencies
pip install -r requirements.txt
```

## Environment Variables

Create a `.env` file in the project root:

```env
GEMINI_API_KEY=your_gemini_api_key_here
OPENWEATHER_API_KEY=optional_weather_api_key
```

## Running the System

### Option 1: Full System
```bash
python run.py
```

### Option 2: Individual Components
```bash
# Backend API
uvicorn backend.api.routes:app --reload --port 8000

# Streamlit Dashboard
streamlit run app/streamlit_app.py
```

## Testing
```bash
pytest tests/ -v
```
