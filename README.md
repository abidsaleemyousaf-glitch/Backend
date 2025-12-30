####### about_api.py  #######
from flask import Flask, jsonify

app = Flask(__name__)

@app.route("/api/about", methods=["GET"])
def get_about_content():
    about_data = {
        "title": "About University Hostel",
        "story": (
            "Government Islamia College, Civil Lines (GICCL) Lahore "
            "was founded in 1947. The hostel provides a safe, secure, "
            "and student-centered environment for academic growth."
        ),

        "stats": [
            {"value": "500+", "label": "Students"},
            {"value": "15+", "label": "Years Experience"},
            {"value": "24/7", "label": "Security"},
            {"value": "98%", "label": "Satisfaction Rate"}
        ],

        "mission": [
            {
                "title": "Student-Centered",
                "description": "We prioritize student welfare and academic success."
            },
            {
                "title": "Safety First",
                "description": "24/7 security, CCTV monitoring, and strict safety rules."
            },
            {
                "title": "Community",
                "description": "A healthy and inclusive student living environment."
            }
        ],

        "team": [
            {"name": "Hussain Jutt", "role": "Hostel Director"},
            {"name": "Mamoon Butt", "role": "Operations Manager"},
            {"name": "Chaudhary Gohar Chadhar", "role": "Student Welfare Officer"},
            {"name": "Ahmad Shazad", "role": "Facilities Manager"}
        ]
    }

    return jsonify(about_data)


if __name__ == "__main__":
    app.run(debug=True)

--------------------------------------------------------------------------------------------------------------------------------------

##### content_api.py #########
from flask import Flask, request, jsonify
import sqlite3

app = Flask(__name__)

# Database connection
def get_db_connection():
    conn = sqlite3.connect("hostel.db")
    conn.row_factory = sqlite3.Row
    return conn


# Create table (run once)
@app.route("/init-db")
def init_db():
    conn = get_db_connection()
    conn.execute("""
        CREATE TABLE IF NOT EXISTS contact_messages (
            id INTEGER PRIMARY KEY AUTOINCREMENT,
            name TEXT NOT NULL,
            email TEXT NOT NULL,
            phone TEXT,
            room_type TEXT,
            message TEXT
        )
    """)
    conn.commit()
    conn.close()
    return "Database Initialized Successfully"


# Contact Form API
@app.route("/api/contact", methods=["POST"])
def submit_contact_form():
    data = request.get_json()

    name = data.get("name")
    email = data.get("email")
    phone = data.get("phone")
    room_type = data.get("roomType")
    message = data.get("message")

    if not name or not email:
        return jsonify({"error": "Name and Email are required"}), 400

    conn = get_db_connection()
    conn.execute("""
        INSERT INTO contact_messages (name, email, phone, room_type, message)
        VALUES (?, ?, ?, ?, ?)
    """, (name, email, phone, room_type, message))
    conn.commit()
    conn.close()

    return jsonify({"success": "Message submitted successfully"}), 201


if __name__ == "__main__":
    app.run(debug=True)

