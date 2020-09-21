# Network Security (NetSec) 2020 User Manual

Welcome to the NetSec course! For this course, GitLab will serve as the main point of (online)
interaction between students and the course team. Concretely, using this GitLab you will

- receive lecture materials such as slides, exercises, and old exams;
- be able to ask question about lecture content, projects, administrative issues, ...;
- have the ability to hand in your exercise sheets and get feedback on them; and
- be able to apply for a _lecture ticket_, which will allow you to visit the lectures at ETH in
  person.

Each of these items are discussed in more detail below.

### Useful Links

- [The issue repository](https://gitlab.inf.ethz.ch/PRV-PERRIG/netsec-course/netsec-2020-issues/-/issues)
- [Course webpage](https://netsec.ethz.ch/courses/netsec-2020/)
- [Course catalog page](http://vvz.ethz.ch/Vorlesungsverzeichnis/lerneinheit.view?lerneinheitId=141460&semkez=2020W&ansicht=KATALOGDATEN)
- [Remote lecture questions](https://course.netsec.inf.ethz.ch/questions)
- [CHN C 14 livestream](https://video.ethz.ch/live/lectures/zentrum/chn/chn-c-14.html)
- [HG F 1 livestream](https://video.ethz.ch/live/lectures/zentrum/hg/hg-f-1/projector.html)
- [Stream recordings](https://video.ethz.ch/lectures/d-infk/2020/autumn/263-4640-00L.html)

## Lecture Tickets

### Lecture Registration

Because we will not be able to use the lecture halls to full capacity this semester, it will
unfortunately not be possible for everyone to visit each lecture. Therefore, we have designed a
system of _lecture tickets_. That is, each week you will be able to request a seat for the upcoming
Tuesday lecture. From the list of all students that requested a seat, our magic algorithm will
automatically select the maximum number of students that are allowed in the room, assign them a
seat, and inform everyone who requested a seat about whether or not a seat was assigned to them, and
if so, which one. In order to keep your calendar plannable, on alternating weeks, an alternating
half of the students will get priority during the seat assignment.

Concretely, every week we will create a GitLab issue for lecture registration. In order to request a
seat, you should give the issue a thumbs up (or participate in the issue in any other way). The seat
assignment then works in two phase, as illustrated in the graphic below.

- **Presale:** Right after each lecture, the presale phase for the next lecture begins. During the
  presale phase, everyone can request a lecture ticket, but no tickets will be issues yet. During
  the presale phase you can also cancel your request by removing your thumbs up from the issue.

- **Registration:** On Monday at 7:00, the registration phase starts. From this point on, lecture
  tickets are automatically handed out to the students who requested a ticket during the presale
  phase. This assignment is in principle random, but in odd weeks students with last names starting
  with the letters `L` through `Z` will be prioritized, and in even weeks students with last names
  starting with the letters `A` through `K`. The registration phase stays open until the end of the
  lecture, and as long as the room is not full, tickets will be created for students who newly
  register using the GitLab issue.

```mermaid
gantt
dateFormat  YYYY-MM-DD:HH
axisFormat  %a
title Lecture Registration Timeline

Presale (L-Z prioritized)  :         des1, 2020-09-08:12,2020-09-14:07
Registration               :         des2, 2020-09-14:07,2020-09-15:12
Lecture 1                  :         des3, 2020-09-15:10,2020-09-15:12
Presale (A-K prioritized)  :         des1, 2020-09-15:12,2020-09-21:07
Registration               :         des2, 2020-09-21:07,2020-09-22:12
Lecture 2                  :         des3, 2020-09-22:10,2020-09-22:12
```

You can always find the currently active lecture registration issue
[here](https://gitlab.inf.ethz.ch/PRV-PERRIG/netsec-course/netsec-2020-issues/-/issues?label_name%5B%5D=lecture-registration).

### Seat Numbering

In order to make sure that we can easily resolve seating conflicts, we have split the lecture hall
into _half rows_. When a seat is assigned to you, you will receive a seat number looking like this:
`4L (3)`. This means that you are assigned a seat in the left half (seen from the back) of the 4th
row. You may sit anywhere in this half row. The `(3)` is intended mainly for our reference to make
certain that lecture tickets are unique.

See the image below to see how the half rows are layed out.

![CHN C 14 room layout](chn_c_14_annotated.jpg)

## Exercise Tickets

Because the exercise room should be large enough to accommodate everyone, we are not running a
ticket system for the exercise sessions. Instead, you can just show up as normal.

## Lecture and Exercise Streams

Every lecture and exercise session will be streamed live and a recording will be made available
through the ETH video portal.

- [Remote lecture questions](https://course.netsec.inf.ethz.ch/questions)
- [CHN C 14 livestream](https://video.ethz.ch/live/lectures/zentrum/chn/chn-c-14.html)
- [HG F 1 livestream](https://video.ethz.ch/live/lectures/zentrum/hg/hg-f-1/projector.html)
- [Stream recordings](https://video.ethz.ch/lectures/d-infk/2020/autumn/263-4640-00L.html)

## Asking Questions

If you want to ask a question about any aspect the course, please use the [issue
tracker](https://gitlab.inf.ethz.ch/PRV-PERRIG/netsec-course/netsec-2020-issues/-/issues).

If your question does not contain personal or sensitive information, we encourage you to make the
issue public. If you are uncomfortable with your question being visible to everyone, you can choose
to make your issue
[confidential](https://docs.gitlab.com/ee/user/project/issues/confidential_issues.html) by selecting
the checkbox on the new issue page. Confidential issues cannot be seen by other students.

If your question does contain personal or sensitive information, please mark the issue as
confidential.

Depending on what the lecture live streams will look like, we might provide another method for more
real-time interaction during the lecture.

***Please do not send us questions by email.***

## Handing in Exercises

You can hand in exercises by creating a **confidential** issue with a title starting with
`[exercise-hand-in]`. You should use the filled in exercise template as the body of the issue.