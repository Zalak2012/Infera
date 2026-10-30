# UBIQUITOUS_LANGUAGE.md

The words this product is about. Every other document (`DESIGN.md`, `TESTING.md`) uses **only** these terms. Capitalised terms in those documents are the ones defined here.

The product helps a **User** prepare for technical interviews by studying **Interview Questions**, practising **DSA Problems**, answering **MCQ Questions**, and re-studying Interview Questions on a spaced-repetition schedule using **Cards**.

## Terms

| Term | Definition | Aliases to avoid | Named in code at |
|---|---|---|---|
| **User** | A registered person with an email and password, who owns all progress and all Cards. | Candidate, Student, Learner, Account, Member | `backend/models/User.js:3` |
| **Interview Question** | A written technical or behavioural question with a model answer, identified by its `questionNumber`. Studied by reading. | Flashcard, Card (a Card is something else), Q&A | `backend/models/InterviewQuestion.js:3` |
| **DSA Problem** | A data-structure/algorithm exercise with a statement, approach, solution and complexities, identified by its `dsaNumber`. | DSA Question, Challenge, Exercise | `backend/models/DSAQuestion.js:3` (class is still called `DSAQuestion`, see Ambiguity 1) |
| **MCQ Question** | A multiple-choice question with exactly one correct option and an explanation, identified by its `questionId` number. | Quiz question, Test, MCQ item | `backend/models/MCQQuestion.js:3` |
| **Quiz** | One sitting in which a User answers MCQ Questions filtered by Category and/or Difficulty. It is a screen, not stored data. | Test, Exam, Assessment | `frontend/src/pages/QuizPage.jsx:45` |
| **Category** | The subject group an Interview Question or an MCQ Question belongs to (e.g. "React", "Arrays"). | Section, Subject, Type | `backend/models/InterviewQuestion.js:20`, `backend/models/MCQQuestion.js:9` |
| **Topic** | The subject group a DSA Problem belongs to (e.g. "Trees"). | Category (for DSA Problems), Chapter | `backend/models/DSAQuestion.js:30` |
| **Difficulty** | How hard a question is: Easy, Medium or Hard. A property of the question, never of the User's answer. | Level, Complexity (complexity means time/space), Quality | `backend/models/InterviewQuestion.js:36` |
| **Seen** | An Interview Question or DSA Problem the User has opened. | Viewed, Read, Visited | `backend/models/User.js:10`, `backend/models/User.js:13` |
| **Attempted** | An MCQ Question the User has answered at least once. | Tried, Taken | `backend/models/User.js:17` |
| **Solved** | A DSA Problem the User has marked as solved (self-reported), or an MCQ Question the User has answered correctly (checked by the system). | Completed, Done, Passed, Correct (as a status) | `backend/models/User.js:14`, `backend/models/User.js:18` |
| **Progress** | The counts of Seen / Attempted / Solved per Category or Topic, shown on the Dashboard and Profile. | Stats (reserved for Card Stats), Analytics, Score | `backend/routes/profileRoutes.js:11` |
| **Card** | One User's spaced-repetition schedule for one Interview Question. Created the first time the User sees that Interview Question. A User has at most one Card per Interview Question. | Flashcard, SRS item, Deck entry | `backend/models/SRSCard.js:3` |
| **Review** | One act of the User answering a Card and rating how well they recalled it. Each Review is appended to the Card's history. | Attempt, Practice, Session, Answer | `backend/routes/srs.js:55`, `backend/models/SRSCard.js:45` |
| **Quality** | The whole number 0-5 the User gives a Review (0 = blackout, 5 = perfect). 3 or more is a **pass**. | Score, Grade, Rating, Difficulty | `backend/utils/srsAlgorithm.js:22`, `backend/models/SRSCard.js:41` |
| **Easiness Factor** | A number (never below 1.3, starts at 2.5) that grows when the User recalls a Card well and shrinks when they do not; it sets how fast the Interval grows. | Ease, Multiplier, EF score | `backend/models/SRSCard.js:23`, `backend/utils/srsAlgorithm.js:36` |
| **Interval** | The number of days until a Card is due again. | Gap, Delay, Period | `backend/models/SRSCard.js:19`, `backend/utils/srsAlgorithm.js:55` |
| **Next Review Date** | The date a Card becomes due: now plus its Interval. | Due date, Schedule, Deadline | `backend/models/SRSCard.js:31` |
| **Due Card** | A Card whose Next Review Date is now or earlier and which is not suspended. | Pending card, Today's card | `backend/routes/srs.js:112` |
| **Suspended Card** | A Card the User has paused; it never appears as a Due Card until resumed. | Archived, Disabled, Hidden | `backend/models/SRSCard.js:59` |
| **Card Status** | Where a Card is in its life: `new` (never reviewed), `learning` (last Review failed), `review` (last Review passed). | State, Stage, Level | `backend/models/SRSCard.js:54`, `backend/utils/srsAlgorithm.js:76` |
| **Card Stats** | Counts of a User's Cards by Card Status, total Reviews, and average Easiness Factor. | Progress (Progress covers Seen/Attempted/Solved) | `backend/routes/srs.js:194` |
| **Token** | The signed login proof (JWT) sent in the `Authorization` header; valid for 7 days. | Session, Auth key, Bearer | `backend/middleware/authMiddleware.js:11` |
| **OTP** | The 6-digit one-time code emailed to a User to prove they own an email address when resetting a password. Valid 10 minutes, single use. | Token (a Token is the login JWT), Code, PIN | `backend/models/OtpToken.js:5` (stored in a model called `OtpToken`, see Ambiguity 6) |
| **Daily Target** | A User's chosen number of new Cards and Reviews per day. **Declared but not yet used by any feature.** | Goal, Quota | `backend/models/User.js:31` |

## Flagged ambiguities

Each entry says where one word means two things, or two words mean one, and **which one wins**. "Wins" means: that is the word used in all documents. Where the code still uses the losing word, it is listed as known naming debt rather than hidden.

1. **"Question" vs "Problem" (two words, one concept: a DSA exercise).** The README, the page title and the UI say "DSA Problems"; the model, seed script and field are `DSAQuestion` / `DSAQuestion.js`. **"DSA Problem" wins.** Reason: a DSA exercise is solved, not answered, and "Question" is already used by two other concepts (Interview Question, MCQ Question). The code identifier `DSAQuestion` is not renamed (renaming a Mongoose model changes the collection name and would orphan existing data); it is listed in the table's "Named in code at" column.

2. **"Category" vs "Topic" (two words, overlapping meaning).** Interview Questions have a `category`; DSA Problems have a `topic`; MCQ Questions have **both** (`category` such as "Arrays", and a finer `topic` such as "Two Pointers", `backend/models/MCQQuestion.js:48`). **Category wins as the general word** for the group an Interview Question or MCQ Question belongs to; **Topic is used only for DSA Problems** (and for the finer label on an MCQ Question, where it is always written as "the MCQ Question's `topic` field"). Note that the 10 MCQ Categories are named like DSA Topics ("Arrays", "Trees", ...) but are separate values in separate collections.

3. **"Solved" means two things.** For a DSA Problem it is **self-reported** (`backend/routes/dsaRoutes.js:46`: the User clicks "solved"); for an MCQ Question it is **system-checked** (`backend/routes/mcqRoutes.js:98`: the chosen option equals `correctAnswerIndex`). **"Solved" wins as the one word** for both, always followed by who decided when it matters ("self-reported" / "checked"). "Completed", "Done" and "Correct" are not used as synonyms.

4. **"Seen" / "Attempted" (two words, one idea: "the User has touched this").** Interview Questions and DSA Problems are *Seen*; MCQ Questions are *Attempted*, because an MCQ is not "seen" until it is answered. **Both words stay**, each tied to its own thing: Seen = opened to read, Attempted = answered.

5. **"Review" means an event, a Card Status and an API route.** `Review` (the act, `backend/routes/srs.js:55`), `status: 'review'` (a Card whose last Review passed, `backend/models/SRSCard.js:56`) and `POST /api/srs/review/:cardId` (the route). **"Review" wins as the event only.** The Card Status is always written "Card Status `review`" (with the backticks) in documents, and the route is always written as a path.

6. **"Token" vs "OTP" (one word, two things).** The login JWT is a **Token**; the emailed 6-digit code is an **OTP**. The model that stores an OTP is called `OtpToken` (`backend/models/OtpToken.js:3`), which blurs the two. **Token = JWT only, OTP = emailed code wins.** `OtpToken` is naming debt in the code.

7. **"Suspended" is a Card Status value *and* a separate boolean.** `status` allows the value `"suspended"` (`backend/models/SRSCard.js:56`) **and** there is a `suspended: Boolean` field (`backend/models/SRSCard.js:59`). Only the boolean is ever written (`backend/routes/srs.js:262`) and only the boolean is queried (`backend/routes/srs.js:119`). **The boolean wins.** A Card Status is therefore only `new`, `learning` or `review`; the `"suspended"` enum value is dead and is not part of the vocabulary.

8. **`questionId` means two different things.** On a Card it is the database `_id` of an Interview Question (`backend/models/SRSCard.js:10`); on an MCQ Question it is a plain number (`backend/models/MCQQuestion.js:4`) and the User's `mcqAttempted` list stores those numbers. **In documents, "Interview Question id" and "MCQ Question number" are written out in full and the bare name `questionId` is never used on its own.**

9. **"Stats" is stored twice.** A User has a cached `srsStats` object (`backend/models/User.js:21`) refreshed after each Review (`backend/routes/srs.js:287`), and `GET /api/srs/stats` recomputes the same counts live (`backend/routes/srs.js:194`). The two can differ (the cached one counts Suspended Cards, the live one does not). **The live Card Stats win** as the authoritative numbers; the cached copy is only used to show a summary on the Profile.

10. **"Quality" vs "Difficulty" vs "score".** Difficulty (Easy/Medium/Hard) describes a question. Quality (0-5) describes how well the User recalled a Card in one Review. A Card also stores the **last** Quality (`backend/models/SRSCard.js:41`) in addition to each Review's own Quality. **Quality and Difficulty are kept strictly separate, "score" is not used.** "Quality" without qualification means the Quality of a Review.
