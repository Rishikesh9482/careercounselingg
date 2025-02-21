# careercounselingg
git init
git add .
git commit -m "First version of my career counseling app"
git remote add origin https://github.com/your-username/your-repo-name.git
git push -u origin main
import React, { useState } from 'react';
import { Card, CardContent } from '@/components/ui/card';
import { Button } from '@/components/ui/button';
import { BarChart, Bar, XAxis, YAxis, Tooltip, PieChart, Pie, Cell, Legend } from 'recharts';

const questions = [
  {
    question: 'Which subject do you enjoy the most?',
    options: ['Mathematics', 'Science', 'Arts', 'Business'],
    scores: [5, 4, 3, 2],
  },
  {
    question: 'What is your preferred working style?',
    options: ['Teamwork', 'Independent', 'Flexible', 'Structured'],
    scores: [4, 3, 5, 2],
  },
  {
    question: 'Which activity sounds most appealing?',
    options: ['Solving puzzles', 'Experimenting', 'Creating art', 'Managing projects'],
    scores: [5, 4, 3, 2],
  },
];

const COLORS = ['#0088FE', '#00C49F', '#FFBB28', '#FF8042'];

export default function CareerCounselingApp() {
  const [answers, setAnswers] = useState(Array(questions.length).fill(null));
  const [submitted, setSubmitted] = useState(false);
  const [score, setScore] = useState(0);
  const [isInstructor, setIsInstructor] = useState(false);
  const [password, setPassword] = useState('');
  const [allResults, setAllResults] = useState([]);

  const handleAnswer = (questionIndex, optionIndex) => {
    const newAnswers = [...answers];
    newAnswers[questionIndex] = optionIndex;
    setAnswers(newAnswers);
  };

  const calculateScore = () => {
    const totalScore = answers.reduce((acc, answer, index) => {
      return acc + (answer !== null ? questions[index].scores[answer] : 0);
    }, 0);
    setScore(totalScore);
    setSubmitted(true);

    const result = {
      answers,
      score: totalScore,
      timestamp: new Date().toLocaleString(),
    };
    const storedResults = JSON.parse(localStorage.getItem('studentResults')) || [];
    storedResults.push(result);
    localStorage.setItem('studentResults', JSON.stringify(storedResults));
  };

  const getRecommendation = () => {
    if (score >= 12) return 'Consider careers in Science, Technology, Engineering, or Mathematics (STEM).';
    if (score >= 8) return 'You might enjoy careers in Creative Arts or Design.';
    return 'Explore careers in Business, Management, or Entrepreneurship.';
  };

  const handleInstructorLogin = () => {
    if (password === 'supersecret') {
      setIsInstructor(true);
      const results = JSON.parse(localStorage.getItem('studentResults')) || [];
      setAllResults(results);
    } else {
      alert('Wrong password!');
    }
  };

  const scoreData = allResults.map((result, index) => ({
    name: `Student ${index + 1}`,
    score: result.score,
  }));

  const careerPaths = {
    STEM: 0,
    Arts: 0,
    Business: 0,
  };

  allResults.forEach((result) => {
    if (result.score >= 12) careerPaths.STEM++;
    else if (result.score >= 8) careerPaths.Arts++;
    else careerPaths.Business++;
  });

  const pieData = [
    { name: 'STEM', value: careerPaths.STEM },
    { name: 'Arts', value: careerPaths.Arts },
    { name: 'Business', value: careerPaths.Business },
  ];

  return (
    <div className="max-w-xl mx-auto p-4">
      {!isInstructor ? (
        <div>
          <h1 className="text-2xl font-bold mb-4">Career Counseling Test</h1>
          {!submitted ? (
            questions.map((q, idx) => (
              <Card key={idx} className="mb-4">
                <CardContent>
                  <h2 className="text-lg font-semibold mb-2">{q.question}</h2>
                  {q.options.map((option, optIdx) => (
                    <Button
                      key={optIdx}
                      variant={answers[idx] === optIdx ? 'default' : 'outline'}
                      onClick={() => handleAnswer(idx, optIdx)}
                      className="m-1"
                    >
                      {option}
                    </Button>
                  ))}
                </CardContent>
              </Card>
            ))
          ) : (
            <Card className="p-4">
              <h2 className="text-xl font-bold">Your Recommendation</h2>
              <p className="mt-2">{getRecommendation()}</p>
            </Card>
          )}

          {!submitted && (
            <Button
              className="w-full mt-4"
              onClick={calculateScore}
              disabled={answers.includes(null)}
            >
              Submit
            </Button>
          )}

          <div className="mt-4">
            <input
              type="password"
              placeholder="Instructor Password"
              value={password}
              onChange={(e) => setPassword(e.target.value)}
              className="border p-2 w-full"
            />
            <Button className="w-full mt-2" onClick={handleInstructorLogin}>
              Instructor Login
            </Button>
          </div>
        </div>
      ) : (
        <div>
          <h1 className="text-2xl font-bold mb-4">Instructor Dashboard</h1>
          {allResults.length === 0 ? (
            <p>No results yet.</p>
          ) : (
            <div>
              <h2 className="text-lg font-semibold mt-4">Student Scores</h2>
              <BarChart width={400} height={300} data={scoreData}>
                <XAxis dataKey="name" />
                <YAxis />
                <Tooltip />
                <Bar dataKey="score" fill="#8884d8" />
              </BarChart>

              <h2 className="text-lg font-semibold mt-6">Career Path Preferences</h2>
              <PieChart width={400} height={300}>
                <Pie
                  data={pieData}
                  cx="50%"
                  cy="50%"
                  outerRadius={100}
                  fill="#8884d8"
                  dataKey="value"
                  label
                >
                  {pieData.map((entry, index) => (
                    <Cell key={`cell-${index}`} fill={COLORS[index % COLORS.length]} />
                  ))}
                </Pie>
                <Tooltip />
                <Legend />
              </PieChart>
            </div>
          )}
        </div>
      )}
    </div>
  );
}
if (password === 'yournewpassword') {
