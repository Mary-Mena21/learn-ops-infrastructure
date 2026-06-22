curl -X POST -H "Content-Type: application/json" \
     -d '{"username": "your_username", "password": "your_password"}' \
     http://localhost:8000/accounts/verify

curl -X POST -H "Content-Type: application/json" \
    -H "Authorization: Token your_token_here" \
        -d '{"studentId": 1, "assessmentId": 1}' \
        http://localhost:8000/assessments


HTTP 400 Bad Request
Allow: GET, POST, HEAD, OPTIONS
Content-Type: application/json
Vary: Accept

{
    "message": "Please provide a studentId query parameter"
}

--------------------------------------------------------
# Bug Fix: Student Assessment Retrieval

## The Bug
[Describe what caused the `'NoneType' object has no attribute 'user'` error.
Which variable was None? Under what condition?]

## Log Statements Added
[Paste the log statements you added, including the log level and message for each.]

## What the Logs Revealed
[What did you observe in the logs that pointed you to the root cause?]

## The Fix
[Describe the change you made to resolve the bug. One or two sentences is fine.]




     -d '{"username": "admin", "password": "admin"}' \
     http://localhost:8000/accounts/verify
{"token":"c79db954080611831d479cd221f71397aba83dcb"


curl -X POST -H "Content-Type: application/json" \
              -H "Authorization: Token c79db954080611831d479cd221f71397aba83dcb" \
              -d '{"studentId": 1, "assessmentId": 1}' \
              http://localhost:8000/assessments



mariana@Mariana-SSC:~/workspace/lms/learn-ops-infrastructure$ curl -v -X POST -H "Content-Type: application/json" \
  -H "Authorization: Token c79db954080611831d479cd221f71397aba83dcb" \
  -d '{"studentId": 1, "assessmentId": 1}' \
  http://localhost:8000/assessments
Note: Unnecessary use of -X or --request, POST is already inferred.
* Host localhost:8000 was resolved.
* IPv6: ::1
* IPv4: 127.0.0.1
*   Trying [::1]:8000...
* Connected to localhost (::1) port 8000
> POST /assessments HTTP/1.1
> Host: localhost:8000
> User-Agent: curl/8.5.0
> Accept: */*
> Content-Type: application/json
> Authorization: Token c79db954080611831d479cd221f71397aba83dcb
> Content-Length: 35
> 
< HTTP/1.1 400 Bad Request
< Date: Mon, 15 Jun 2026 17:00:14 GMT
< Server: WSGIServer/0.2 CPython/3.11.11
< Content-Type: application/json
< Vary: Accept, origin
< Allow: GET, POST, HEAD, OPTIONS
< X-Frame-Options: DENY
< Content-Length: 194
< X-Content-Type-Options: nosniff
< Referrer-Policy: same-origin
< Cross-Origin-Opener-Policy: same-origin
< Server-Timing: TimerPanel_utime;dur=64.47599999998488;desc="User CPU time", TimerPanel_stime;dur=2.8329999999954225;desc="System CPU time", TimerPanel_total;dur=67.3089999999803;desc="Total CPU time", TimerPanel_total_time;dur=90.99110099487007;desc="Elapsed time", SQLPanel_sql_time;dur=19.89707298344001;desc="SQL 7 queries", CachePanel_total_time;dur=0;desc="Cache 0 Calls"
< 
* Connection #0 to host localhost left intact
{"reason":"duplicate key value violates unique constraint \"LearningAPI_studentasses_student_id_assessment_id_4e995601_uniq\"\nDETAIL:  Key (student_id, assessment_id)=(1, 1) already exists.\n"}mariana@Mariana-SSC:~/workspace/lms/learn-ops-infrastructure$ 