# detect.py
import psycopg2

DB_PARAMS = {
    "dbname": "anomaly_db",
    "user": "postgres",
    "password": "password",
    "host": "localhost",
    "port": 5432
}

def detect_anomalies():
    conn = psycopg2.connect(**DB_PARAMS)
    cur = conn.cursor()
    query = """
        SELECT user_id, COUNT(*) AS logins
        FROM logins
        WHERE login_time > now() - interval '1 hour'
        GROUP BY user_id
        HAVING COUNT(*) > 10;
    """
    cur.execute(query)
    anomalies = cur.fetchall()
    print("Anomalies detected:", anomalies)
    cur.close()
    conn.close()

if __name__ == "__main__":
    detect_anomalies()
-- schema.sql
CREATE TABLE logins (
    id SERIAL PRIMARY KEY,
    user_id INT,
    login_time TIMESTAMP DEFAULT now(),
    ip_address TEXT
);

-- Insert sample logins
INSERT INTO logins(user_id, ip_address) VALUES
(1, '192.168.1.1'),
(1, '192.168.1.2'),
(2, '192.168.1.3');
