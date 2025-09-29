# url-shortener-flask
url-shortener-flask
# URL Shortener (Flask)
Basit kısa link üretici (in-memory).
from flask import Flask, request, redirect, render_template_string
import uuid

app = Flask(__name__)
db = {}

TEMPLATE = '''
<!doctype html>
<title>URL Shortener</title>
<h1>URL Shortener</h1>
<form method=post>
  <input name=url placeholder="https://example.com" style="width:300px">
  <button type=submit>Shorten</button>
</form>
{% if short %}<p>Short URL: <a href="{{short}}">{{short}}</a></p>{% endif %}
'''

@app.route("/", methods=["GET","POST"])
def index():
    short = None
    if request.method == "POST":
        long = request.form.get("url")
        key = uuid.uuid4().hex[:6]
        db[key] = long
        short = request.host_url + key
    return render_template_string(TEMPLATE, short=short)

@app.route("/<key>")
def go(key):
    long = db.get(key)
    if long:
        return redirect(long)
    return "Bulunamadı", 404

if __name__ == "__main__":
    app.run(debug=True)
