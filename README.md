import React, { useState, useEffect } from "react";
import { Line, Bar } from "react-chartjs-2";
import Chart from 'chart.js/auto';

export default function SpeakUpApp() {
  const [started, setStarted] = useState(false);
  const [activeFeature, setActiveFeature] = useState(null);
  const [experiences, setExperiences] = useState([]);
  const [skills, setSkills] = useState([]);
  const [experienceInput, setExperienceInput] = useState("");
  const [skillInput, setSkillInput] = useState("");
  const [signedUp, setSignedUp] = useState(false);
  const [loginMode, setLoginMode] = useState(false);
  const [userData, setUserData] = useState({ name: "", email: "", password: "" });
  const [resumeFeedback, setResumeFeedback] = useState("");
  const [feedbackSubmitted, setFeedbackSubmitted] = useState(false);
  const [showCelebration, setShowCelebration] = useState(false);
  const [showLogoutPopup, setShowLogoutPopup] = useState(false);
  const [gdMessages, setGdMessages] = useState([]);
  const [gdMessageInput, setGdMessageInput] = useState("");
  const [interviewType, setInterviewType] = useState("technical");
  const [practiceDuration, setPracticeDuration] = useState(30);
  const [aiQuestion, setAiQuestion] = useState("");
  const [aiResponse, setAiResponse] = useState("");
  const [practiceHistory, setPracticeHistory] = useState([]);

  // Mock data for improvement graph
  const [improvementData, setImprovementData] = useState({
    labels: ['Week 1', 'Week 2', 'Week 3', 'Week 4', 'Week 5'],
    datasets: [
      {
        label: 'Confidence Level',
        data: [3, 4, 5, 6, 7],
        borderColor: 'rgba(75, 192, 192, 1)',
        backgroundColor: 'rgba(75, 192, 192, 0.2)',
        tension: 0.1
      },
      {
        label: 'Fluency Score',
        data: [4, 5, 6, 6, 8],
        borderColor: 'rgba(153, 102, 255, 1)',
        backgroundColor: 'rgba(153, 102, 255, 0.2)',
        tension: 0.1
      }
    ]
  });

  const interviewOptions = [
    { id: 1, type: "technical", name: "Technical Interview", price: 500, duration: 45 },
    { id: 2, type: "behavioral", name: "Behavioral Interview", price: 400, duration: 30 },
    { id: 3, type: "hr", name: "HR Interview", price: 300, duration: 25 },
    { id: 4, type: "case", name: "Case Study Interview", price: 600, duration: 60 }
  ];

  const practiceOptions = [
    { id: 1, type: "general", name: "General Practice", price: 200, duration: 30 },
    { id: 2, type: "presentation", name: "Presentation Practice", price: 300, duration: 45 },
    { id: 3, type: "elevator", name: "Elevator Pitch", price: 150, duration: 15 }
  ];

  useEffect(() => {
    const name = localStorage.getItem("speakup-name");
    const email = localStorage.getItem("speakup-email");
    if (name && email) {
      setUserData(prev => ({ ...prev, name, email }));
      setSignedUp(true);
      
      // Load mock practice history
      setPracticeHistory([
        { date: "2023-05-01", type: "GD", duration: 30, score: 7 },
        { date: "2023-05-03", type: "Interview", duration: 45, score: 8 },
        { date: "2023-05-05", type: "Practice", duration: 30, score: 6 },
        { date: "2023-05-08", type: "GD", duration: 45, score: 8 },
        { date: "2023-05-10", type: "Interview", duration: 60, score: 9 },
      ]);
    }
  }, []);

  const handleAddExperience = () => {
    if (experienceInput.trim() && !experiences.includes(experienceInput.trim())) {
      setExperiences([...experiences, experienceInput.trim()]);
      setExperienceInput("");
    }
  };

  const handleAddSkill = () => {
    if (skillInput.trim() && !skills.includes(skillInput.trim())) {
      setSkills([...skills, skillInput.trim()]);
      setSkillInput("");
    }
  };

  const handleFeedbackSubmit = () => {
    if (experiences.length && skills.length && resumeFeedback.trim()) {
      setFeedbackSubmitted(true);
      setExperiences([]);
      setSkills([]);
      setResumeFeedback("");
    } else {
      alert("Please complete all fields before submitting.");
    }
  };

  const handleCelebrate = () => {
    setShowCelebration(true);
    setTimeout(() => setShowCelebration(false), 3000);
  };

  const handleLogout = () => {
    localStorage.removeItem("speakup-name");
    localStorage.removeItem("speakup-email");
    setUserData({ name: "", email: "", password: "" });
    setSignedUp(false);
    setStarted(false);
    setActiveFeature(null);
  };

  const handleSignup = () => {
    localStorage.setItem("speakup-name", userData.name);
    localStorage.setItem("speakup-email", userData.email);
    setSignedUp(true);
    setLoginMode(false);
  };

  const handleLogin = () => {
    // In a real app, you would verify credentials here
    setSignedUp(true);
    setLoginMode(false);
  };

  const handleGdMessageSend = () => {
    if (gdMessageInput.trim()) {
      setGdMessages([...gdMessages, {
        id: Date.now(),
        text: gdMessageInput,
        sender: "You",
        time: new Date().toLocaleTimeString()
      }]);
      setGdMessageInput("");
      
      // Simulate response after 1 second
      setTimeout(() => {
        setGdMessages(prev => [...prev, {
          id: Date.now() + 1,
          text: "That's an interesting point! What do others think?",
          sender: "AI Moderator",
          time: new Date().toLocaleTimeString()
        }]);
      }, 1000);
    }
  };

  const handleAiQuestionSubmit = () => {
    if (aiQuestion.trim()) {
      setAiResponse("Thinking...");
      // Simulate AI response after 1 second
      setTimeout(() => {
        setAiResponse("Based on your question, I recommend practicing common interview questions and focusing on clear communication. Would you like me to suggest some specific exercises?");
      }, 1000);
    }
  };

  const renderFeature = () => {
    switch (activeFeature) {
      case "gd":
        return (
          <div className="max-w-3xl w-full bg-white p-6 rounded-lg shadow-lg">
            <h2 className="text-2xl font-bold mb-4 text-teal-700">Group Discussion</h2>
            <div className="mb-4 p-4 border rounded-lg h-64 overflow-y-auto bg-gray-50">
              {gdMessages.length > 0 ? (
                gdMessages.map(msg => (
                  <div key={msg.id} className="mb-2 text-left">
                    <p className="font-semibold">{msg.sender} <span className="text-xs text-gray-500">{msg.time}</span></p>
                    <p className="bg-white p-2 rounded shadow-sm">{msg.text}</p>
                  </div>
                ))
              ) : (
                <p className="text-gray-500">Start the discussion by sending a message!</p>
              )}
            </div>
            <div className="flex">
              <input
                type="text"
                value={gdMessageInput}
                onChange={(e) => setGdMessageInput(e.target.value)}
                placeholder="Type your message..."
                className="flex-1 border rounded-l px-3 py-2"
                onKeyPress={(e) => e.key === 'Enter' && handleGdMessageSend()}
              />
              <button
                onClick={handleGdMessageSend}
                className="bg-teal-600 text-white px-4 py-2 rounded-r"
              >
                Send
              </button>
            </div>
            <div className="mt-6">
              <h3 className="font-semibold mb-2">Participants</h3>
              <div className="flex space-x-2">
                <span className="bg-blue-100 px-3 py-1 rounded-full">You</span>
                <span className="bg-green-100 px-3 py-1 rounded-full">Alex</span>
                <span className="bg-purple-100 px-3 py-1 rounded-full">Priya</span>
                <span className="bg-yellow-100 px-3 py-1 rounded-full">Jordan</span>
              </div>
            </div>
            <button
              className="mt-4 bg-gray-200 px-4 py-2 rounded"
              onClick={() => setActiveFeature(null)}
            >
              Back to Menu
            </button>
          </div>
        );
      case "interview":
        return (
          <div className="max-w-3xl w-full bg-white p-6 rounded-lg shadow-lg">
            <h2 className="text-2xl font-bold mb-4 text-teal-700">Mock Interviews</h2>
            <div className="mb-6">
              <h3 className="font-semibold mb-2">Select Interview Type</h3>
              <div className="grid grid-cols-1 sm:grid-cols-2 gap-4">
                {interviewOptions.map(option => (
                  <div 
                    key={option.id} 
                    className={`border rounded-lg p-4 cursor-pointer ${interviewType === option.type ? 'border-teal-500 bg-teal-50' : 'border-gray-200'}`}
                    onClick={() => setInterviewType(option.type)}
                  >
                    <h4 className="font-bold">{option.name}</h4>
                    <p>Duration: {option.duration} mins</p>
                    <p className="font-semibold">Price: ₹{option.price}</p>
                  </div>
                ))}
              </div>
            </div>
            <div className="mb-6">
              <h3 className="font-semibold mb-2">Available Slots</h3>
              <div className="grid grid-cols-3 gap-2">
                {["10:00 AM", "1:00 PM", "4:00 PM", "11:00 AM", "2:00 PM", "5:00 PM"].map(slot => (
                  <button 
                    key={slot}
                    className="border border-teal-500 rounded px-3 py-1 hover:bg-teal-50"
                  >
                    {slot}
                  </button>
                ))}
              </div>
            </div>
            <button className="bg-teal-600 text-white px-6 py-2 rounded-lg">
              Book Interview for ₹{interviewOptions.find(o => o.type === interviewType)?.price || 0}
            </button>
            <button
              className="mt-4 ml-4 bg-gray-200 px-4 py-2 rounded"
              onClick={() => setActiveFeature(null)}
            >
              Back to Menu
            </button>
          </div>
        );
      case "practice":
        return (
          <div className="max-w-3xl w-full bg-white p-6 rounded-lg shadow-lg">
            <h2 className="text-2xl font-bold mb-4 text-teal-700">Practice Sessions</h2>
            <div className="mb-6">
              <h3 className="font-semibold mb-2">Select Practice Type</h3>
              <div className="grid grid-cols-1 sm:grid-cols-3 gap-4">
                {practiceOptions.map(option => (
                  <div 
                    key={option.id} 
                    className="border border-gray-200 rounded-lg p-4 hover:border-teal-500 cursor-pointer"
                    onClick={() => setPracticeDuration(option.duration)}
                  >
                    <h4 className="font-bold">{option.name}</h4>
                    <p>Duration: {option.duration} mins</p>
                    <p className="font-semibold">Price: ₹{option.price}</p>
                  </div>
                ))}
              </div>
            </div>
            <div className="mb-4">
              <label className="block font-semibold mb-1">Select Duration (minutes)</label>
              <input 
                type="range" 
                min="15" 
                max="60" 
                step="15"
                value={practiceDuration}
                onChange={(e) => setPracticeDuration(parseInt(e.target.value))}
                className="w-full"
              />
              <div className="flex justify-between">
                <span>15 min</span>
                <span>30 min</span>
                <span>45 min</span>
                <span>60 min</span>
              </div>
            </div>
            <button className="bg-teal-600 text-white px-6 py-2 rounded-lg">
              Start Practice Session (₹{Math.round(practiceDuration * 6.66)})
            </button>
            <button
              className="mt-4 ml-4 bg-gray-200 px-4 py-2 rounded"
              onClick={() => setActiveFeature(null)}
            >
              Back to Menu
            </button>
          </div>
        );
      case "graph":
        return (
          <div className="max-w-4xl w-full bg-white p-6 rounded-lg shadow-lg">
            <h2 className="text-2xl font-bold mb-4 text-teal-700">Your Improvement Graph</h2>
            
            {practiceHistory.length > 0 ? (
              <>
                <div className="mb-8">
                  <h3 className="font-semibold mb-2">Communication Skills Progress</h3>
                  <div className="h-64">
                    <Line 
                      data={improvementData}
                      options={{
                        responsive: true,
                        maintainAspectRatio: false,
                        scales: {
                          y: {
                            beginAtZero: true,
                            max: 10
                          }
                        }
                      }}
                    />
                  </div>
                </div>
                
                <div className="mb-8">
                  <h3 className="font-semibold mb-2">Practice Frequency</h3>
                  <div className="h-64">
                    <Bar
                      data={{
                        labels: practiceHistory.map(session => session.date),
                        datasets: [{
                          label: 'Duration (minutes)',
                          data: practiceHistory.map(session => session.duration),
                          backgroundColor: 'rgba(54, 162, 235, 0.5)',
                          borderColor: 'rgba(54, 162, 235, 1)',
                          borderWidth: 1
                        }]
                      }}
                      options={{
                        responsive: true,
                        maintainAspectRatio: false,
                        scales: {
                          y: {
                            beginAtZero: true
                          }
                        }
                      }}
                    />
                  </div>
                </div>
                
                <div className="mb-4">
                  <h3 className="font-semibold mb-2">Recent Practice Sessions</h3>
                  <table className="w-full border-collapse">
                    <thead>
                      <tr className="bg-gray-100">
                        <th className="border p-2">Date</th>
                        <th className="border p-2">Type</th>
                        <th className="border p-2">Duration</th>
                        <th className="border p-2">Score</th>
                      </tr>
                    </thead>
                    <tbody>
                      {practiceHistory.map((session, index) => (
                        <tr key={index} className={index % 2 === 0 ? 'bg-white' : 'bg-gray-50'}>
                          <td className="border p-2">{session.date}</td>
                          <td className="border p-2">{session.type}</td>
                          <td className="border p-2">{session.duration} mins</td>
                          <td className="border p-2">{session.score}/10</td>
                        </tr>
                      ))}
                    </tbody>
                  </table>
                </div>
              </>
            ) : (
              <div className="text-center py-8">
                <p className="text-lg text-gray-600 mb-4">You haven't started any practice sessions yet.</p>
                <button 
                  className="bg-teal-600 text-white px-6 py-2 rounded-lg"
                  onClick={() => setActiveFeature("practice")}
                >
                  Start Practicing Now
                </button>
              </div>
            )}
            
            <button
              className="mt-4 bg-gray-200 px-4 py-2 rounded"
              onClick={() => setActiveFeature(null)}
            >
              Back to Menu
            </button>
          </div>
        );
      case "feedback":
        return (
          <div className="max-w-xl w-full bg-white p-6 rounded-lg shadow-lg">
            <h2 className="text-2xl font-bold mb-4 text-teal-700">Get Resume Improvement Tips</h2>

            <div className="mb-4 text-left">
              <label className="block text-gray-700 font-semibold mb-1">Work Experience</label>
              <input
                type="text"
                value={experienceInput}
                onChange={(e) => setExperienceInput(e.target.value)}
                placeholder="e.g. Software Engineer at Google"
                className="w-full border rounded px-3 py-2"
              />
              <button
                onClick={handleAddExperience}
                className="mt-2 bg-gray-200 px-4 py-1 rounded"
              >
                Add Work Experience
              </button>
              <ul className="mt-2 list-disc list-inside text-gray-600">
                {experiences.map((exp, index) => (
                  <li key={index}>{exp}</li>
                ))}
              </ul>
            </div>

            <div className="mb-4 text-left">
              <label className="block text-gray-700 font-semibold mb-1">Skills</label>
              <input
                type="text"
                value={skillInput}
                onChange={(e) => setSkillInput(e.target.value)}
                placeholder="e.g. React, Node.js"
                className="w-full border rounded px-3 py-2"
              />
              <button
                onClick={handleAddSkill}
                className="mt-2 bg-gray-200 px-4 py-1 rounded"
              >
                Add Skill
              </button>
              <ul className="mt-2 list-disc list-inside text-gray-600">
                {skills.map((skill, index) => (
                  <li key={index}>{skill}</li>
                ))}
              </ul>
            </div>

            <div className="mb-6 text-left">
              <label className="block text-gray-700 font-semibold mb-1">Feedback</label>
              <textarea
                placeholder="e.g. Too long, not enough focus on achievements"
                className="w-full border rounded px-3 py-2"
                rows="3"
                value={resumeFeedback}
                onChange={(e) => setResumeFeedback(e.target.value)}
              ></textarea>
            </div>

            {feedbackSubmitted ? (
              <p className="text-green-600 font-semibold mb-4">Thanks! Your feedback has been submitted.</p>
            ) : null}

            <div className="flex space-x-4">
              <button
                className="bg-teal-600 text-white px-4 py-2 rounded"
                onClick={handleFeedbackSubmit}
              >
                Get Resume Tips
              </button>
              <button
                className="bg-gray-200 px-4 py-2 rounded"
                onClick={() => setActiveFeature(null)}
              >
                Back to Menu
              </button>
            </div>
          </div>
        );
      case "ai":
        return (
          <div className="max-w-xl w-full bg-white p-6 rounded-lg shadow-lg">
            <h2 className="text-2xl font-bold mb-4 text-teal-700">AI Communication Coach</h2>
            
            <div className="mb-4 p-4 border rounded-lg h-64 overflow-y-auto bg-gray-50">
              {aiResponse ? (
                <div>
                  <p className="font-semibold">You: {aiQuestion}</p>
                  <p className="mt-2 bg-white p-2 rounded shadow-sm">
                    <span className="font-semibold">AI Coach: </span>
                    {aiResponse}
                  </p>
                </div>
              ) : (
                <p className="text-gray-500">Ask me anything about communication skills, interviews, or public speaking!</p>
              )}
            </div>
            
            <div className="flex">
              <input
                type="text"
                value={aiQuestion}
                onChange={(e) => setAiQuestion(e.target.value)}
                placeholder="Ask a question..."
                className="flex-1 border rounded-l px-3 py-2"
                onKeyPress={(e) => e.key === 'Enter' && handleAiQuestionSubmit()}
              />
              <button
                onClick={handleAiQuestionSubmit}
                className="bg-teal-600 text-white px-4 py-2 rounded-r"
              >
                Ask
              </button>
            </div>
            
            <div className="mt-4">
              <h3 className="font-semibold mb-2">Suggested Questions:</h3>
              <div className="flex flex-wrap gap-2">
                {[
                  "How can I improve my confidence?",
                  "What are common interview mistakes?",
                  "Tips for group discussions",
                  "How to structure my answers?"
                ].map((q, i) => (
                  <button
                    key={i}
                    onClick={() => setAiQuestion(q)}
                    className="bg-gray-100 hover:bg-gray-200 px-3 py-1 rounded-full text-sm"
                  >
                    {q}
                  </button>
                ))}
              </div>
            </div>
            
            <button
              className="mt-4 bg-gray-200 px-4 py-2 rounded"
              onClick={() => setActiveFeature(null)}
            >
              Back to Menu
            </button>
          </div>
        );
      default:
        return (
          <div className="grid grid-cols-1 sm:grid-cols-2 md:grid-cols-3 gap-6 max-w-6xl w-full px-4">
            <div className="bg-white rounded-lg shadow-md p-6 hover:shadow-lg transition">
              <h2 className="text-xl font-semibold text-teal-700 mb-2">Group Discussions</h2>
              <p className="text-gray-600 mb-4">
                Join or create a room for practicing group discussions with others.
              </p>
              <button
                className="bg-teal-600 text-white px-4 py-2 rounded hover:bg-teal-700"
                onClick={() => setActiveFeature("gd")}
              >
                Join Discussion
              </button>
            </div>

            <div className="bg-white rounded-lg shadow-md p-6 hover:shadow-lg transition">
              <h2 className="text-xl font-semibold text-teal-700 mb-2">Mock Interviews</h2>
              <p className="text-gray-600 mb-4">
                Prepare for real-world interviews with mock interview sessions.
              </p>
              <button
                className="bg-teal-600 text-white px-4 py-2 rounded hover:bg-teal-700"
                onClick={() => setActiveFeature("interview")}
              >
                Start Interview
              </button>
            </div>

            <div className="bg-white rounded-lg shadow-md p-6 hover:shadow-lg transition">
              <h2 className="text-xl font-semibold text-teal-700 mb-2">Practice Sessions</h2>
              <p className="text-gray-600 mb-4">
                Practice your speaking skills with AI-generated prompts or timed tasks.
              </p>
              <button
                className="bg-teal-600 text-white px-4 py-2 rounded hover:bg-teal-700"
                onClick={() => setActiveFeature("practice")}
              >
                Start Practice
              </button>
            </div>

            <div className="bg-white rounded-lg shadow-md p-6 hover:shadow-lg transition">
              <h2 className="text-xl font-semibold text-teal-700 mb-2">Improvement Graph</h2>
              <p className="text-gray-600 mb-4">
                Visualize your progress and improvements over time in communication.
              </p>
              <button
                className="bg-teal-600 text-white px-4 py-2 rounded hover:bg-teal-700"
                onClick={() => setActiveFeature("graph")}
              >
                View Graph
              </button>
            </div>

            <div className="bg-white rounded-lg shadow-md p-6 hover:shadow-lg transition">
              <h2 className="text-xl font-semibold text-teal-700 mb-2">Feedback Center</h2>
              <p className="text-gray-600 mb-4">
                Get constructive feedback from peers and mentors.
              </p>
              <button
                className="bg-teal-600 text-white px-4 py-2 rounded hover:bg-teal-700"
                onClick={() => setActiveFeature("feedback")}
              >
                View Feedback
              </button>
            </div>

            <div className="bg-white rounded-lg shadow-md p-6 hover:shadow-lg transition">
              <h2 className="text-xl font-semibold text-teal-700 mb-2">AI Communication Coach</h2>
              <p className="text-gray-600 mb-4">
                Get instant answers to your communication and interview questions.
              </p>
              <button
                className="bg-teal-600 text-white px-4 py-2 rounded hover:bg-teal-700"
                onClick={() => setActiveFeature("ai")}
              >
                Ask AI Coach
              </button>
            </div>
          </div>
        );
    }
  };

  const LogoutPopup = () => (
    <div className="fixed inset-0 flex items-center justify-center bg-black bg-opacity-50 z-50">
      <div className="bg-white p-6 rounded-lg shadow-xl text-center">
        <h2 className="text-xl font-bold mb-4">Confirm Logout</h2>
        <p className="mb-6">Are you sure you want to log out?</p>
        <div className="flex justify-center space-x-4">
          <button
            className="bg-red-600 text-white px-4 py-2 rounded"
            onClick={handleLogout}
          >
            Yes, Log Out
          </button>
          <button
            className="bg-gray-400 text-white px-4 py-2 rounded"
            onClick={() => setShowLogoutPopup(false)}
          >
            Cancel
          </button>
        </div>
      </div>
    </div>
  );

  const LogoutButton = () => (
    <button
      className="absolute top-4 right-4 bg-red-600 text-white px-4 py-2 rounded shadow hover:bg-red-700 transition"
      onClick={() => setShowLogoutPopup(true)}
    >
      Log Out
    </button>
  );

  const AuthForm = () => (
    <div className="flex flex-col justify-center items-center h-screen">
      <h1 className="text-3xl font-bold text-teal-700 mb-6">
        {loginMode ? "Login to SpeakUp" : "Sign Up for SpeakUp"}
      </h1>
      <div className="bg-white p-6 rounded-lg shadow-md w-full max-w-md">
        {!loginMode && (
          <input
            type="text"
            placeholder="Full Name"
            value={userData.name}
            onChange={(e) => setUserData({ ...userData, name: e.target.value })}
            className="w-full mb-4 px-4 py-2 border rounded"
          />
        )}
        <input
          type="email"
          placeholder="Email Address"
          value={userData.email}
          onChange={(e) => setUserData({ ...userData, email: e.target.value })}
          className="w-full mb-4 px-4 py-2 border rounded"
        />
        <input
          type="password"
          placeholder="Password"
          value={userData.password}
          onChange={(e) => setUserData({ ...userData, password: e.target.value })}
          className="w-full mb-4 px-4 py-2 border rounded"
        />
        <button
          onClick={loginMode ? handleLogin : handleSignup}
          className="w-full bg-teal-700 text-white px-6 py-2 rounded-md hover:bg-teal-800 transition"
        >
          {loginMode ? "Login" : "Sign Up"}
        </button>
        <p className="mt-4 text-center">
          {loginMode ? "Don't have an account? " : "Already have an account? "}
          <button
            onClick={() => setLoginMode(!loginMode)}
            className="text-teal-700 font-semibold"
          >
            {loginMode ? "Sign up" : "Login"}
          </button>
        </p>
      </div>
    </div>
  );

  return (
    <div className="min-h-screen bg-gray-50 text-center p-6 relative">
      {showCelebration && (
        <div className="fixed inset-0 z-50 flex justify-center items-center bg-white bg-opacity-75">
          <div className="text-4xl text-yellow-500 animate-bounce">🎉🎉🎉</div>
        </div>
      )}

      {showLogoutPopup && <LogoutPopup />}
      {signedUp && <LogoutButton />}

      {!signedUp ? (
        <AuthForm />
      ) : !started ? (
        <div className="flex flex-col justify-center items-center h-screen">
          <h1 className="text-4xl font-bold mb-4">
            Welcome to <span className="text-teal-700">SpeakUp</span>, {userData.name}!
          </h1>
          <p className="text-lg text-gray-800 mb-6">
            Practice Group Discussions and Mock Interviews to enhance your communication skills.
          </p>
          <button
            onClick={() => setStarted(true)}
            className="bg-teal-700 text-white px-6 py-3 rounded-md text-lg hover:bg-teal-800 transition"
          >
            Get Started
          </button>
        </div>
      ) : (
        <div className="flex flex-col items-center justify-center min-h-screen space-y-8 pt-16">
          <h1 className="text-4xl font-bold text-teal-700">SpeakSpace</h1>
          <p className="text-lg max-w-2xl text-gray-700 text-center">
            {activeFeature ? "Enhance your communication skills" : "Choose a feature to begin:"}
          </p>

          {renderFeature()}
        </div>
      )}
    </div>
  );
}
