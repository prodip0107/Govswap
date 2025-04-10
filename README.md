# Govswap
Government Transfer Match Portal
// Full updated code with in-app chat integration
import { Card, CardContent } from "@/components/ui/card";
import { Button } from "@/components/ui/button";
import { Input } from "@/components/ui/input";
import { useState } from "react";

const dummyUsers = [
  {
    name: "Amit Sharma",
    employeeId: "EMP1021",
    password: "password123",
    email: "amit.sharma@example.com",
    phone: "9876543210",
    department: "Health",
    designation: "Nurse",
    currentStation: "Kolkata",
    desiredStation: "Asansol",
  },
  {
    name: "Neha Singh",
    employeeId: "EMP2043",
    password: "pass456",
    email: "neha.singh@example.com",
    phone: "9123456780",
    department: "Education",
    designation: "Teacher",
    currentStation: "Asansol",
    desiredStation: "Kolkata",
  },
];

const dummyAllUsers = [
  ...dummyUsers,
  {
    name: "Ravi Kumar",
    employeeId: "EMP3056",
    password: "ravi789",
    email: "ravi.kumar@example.com",
    phone: "9001234567",
    department: "Police",
    designation: "Constable",
    currentStation: "Howrah",
    desiredStation: "Siliguri",
  },
];

export default function HomePage() {
  const [formData, setFormData] = useState({
    name: "",
    employeeId: "",
    password: "",
    email: "",
    phone: "",
    department: "",
    designation: "",
    currentStation: "",
    desiredStation: "",
  });
  const [showDashboard, setShowDashboard] = useState(false);
  const [matches, setMatches] = useState([]);
  const [chatMessages, setChatMessages] = useState([]);
  const [activeChatUser, setActiveChatUser] = useState(null);
  const [requests, setRequests] = useState([]);
  const [isAdmin, setIsAdmin] = useState(false);
  const [searchQuery, setSearchQuery] = useState("");
  const [isEditing, setIsEditing] = useState(false);
  const [loggedInUser, setLoggedInUser] = useState(null);
  const [chatInput, setChatInput] = useState("");

  const handleChange = (e) => {
    setFormData({ ...formData, [e.target.name]: e.target.value });
  };

  const handleSubmit = (e) => {
    e.preventDefault();
    const foundMatches = dummyUsers.filter(
      (user) =>
        user.currentStation === formData.desiredStation &&
        user.desiredStation === formData.currentStation &&
        user.department === formData.department
    );
    setMatches(foundMatches);
    setShowDashboard(true);
    setIsAdmin(false);
    setLoggedInUser(formData);
  };

  const handleLogin = (e) => {
    e.preventDefault();
    const loginId = e.target.loginId.value;
    const password = e.target.password.value;
    if (loginId === "admin" && password === "admin123") {
      setIsAdmin(true);
      setShowDashboard(true);
      return;
    }
    const foundUser = dummyUsers.find(
      (user) => user.employeeId === loginId && user.password === password
    );
    if (foundUser) {
      setShowDashboard(true);
      setIsAdmin(false);
      setFormData(foundUser);
      setLoggedInUser(foundUser);
    } else {
      alert("Invalid login credentials");
    }
  };

  const handleSendRequest = (match) => {
    setRequests([...requests, { to: match.name, status: "Pending" }]);
  };

  const handleAccept = (index) => {
    const updatedRequests = [...requests];
    updatedRequests[index].status = "Accepted";
    setRequests(updatedRequests);
  };

  const handleReject = (index) => {
    const updatedRequests = [...requests];
    updatedRequests[index].status = "Rejected";
    setRequests(updatedRequests);
  };

  const startChat = (user) => {
    setActiveChatUser(user);
    setChatMessages([]);
  };

  const sendMessage = () => {
    if (chatInput.trim() && activeChatUser) {
      setChatMessages([...chatMessages, { sender: loggedInUser.name, text: chatInput }]);
      setChatInput("");
    }
  };

  if (showDashboard) {
    return (
      <div className="min-h-screen bg-white p-6">
        <h1 className="text-3xl font-bold text-blue-800 mb-6">Dashboard</h1>

        <Card className="mb-4">
          <CardContent className="p-4">
            <h2 className="text-xl font-semibold mb-2">Potential Matches</h2>
            {matches.map((match, index) => (
              <div key={index} className="mb-4 p-3 border rounded-lg">
                <p><strong>Name:</strong> {match.name}</p>
                <p><strong>Department:</strong> {match.department}</p>
                <p><strong>Current Station:</strong> {match.currentStation}</p>
                <p><strong>Wants to go:</strong> {match.desiredStation}</p>
                <div className="flex gap-2 mt-2">
                  <Button onClick={() => handleSendRequest(match)}>Send Mutual Request</Button>
                  <Button variant="outline" onClick={() => startChat(match)}>Chat</Button>
                </div>
              </div>
            ))}
          </CardContent>
        </Card>

        {activeChatUser && (
          <Card className="mb-4">
            <CardContent className="p-4">
              <h2 className="text-xl font-semibold mb-2">Chat with {activeChatUser.name}</h2>
              <div className="border h-48 p-2 mb-2 overflow-y-auto bg-gray-50">
                {chatMessages.map((msg, i) => (
                  <div key={i} className="mb-1">
                    <strong>{msg.sender}:</strong> {msg.text}
                  </div>
                ))}
              </div>
              <div className="flex gap-2">
                <Input value={chatInput} onChange={(e) => setChatInput(e.target.value)} placeholder="Type a message..." />
                <Button onClick={sendMessage}>Send</Button>
              </div>
            </CardContent>
          </Card>
        )}
      </div>
    );
  }

  return (
    <div className="min-h-screen bg-gray-100 flex flex-col items-center justify-center p-4">
      <Card className="w-full max-w-xl shadow-lg mb-6">
        <CardContent className="p-6">
          <h1 className="text-2xl font-bold mb-4 text-center text-blue-700">
            GovSwap - Mutual Transfer Portal
          </h1>
          <form onSubmit={handleSubmit} className="space-y-4">
            <Input placeholder="Full Name" name="name" value={formData.name} onChange={handleChange} required />
            <Input placeholder="Employee ID" name="employeeId" value={formData.employeeId} onChange={handleChange} required />
            <Input type="password" placeholder="Password" name="password" value={formData.password} onChange={handleChange} required />
            <Input type="email" placeholder="Email Address" name="email" value={formData.email} onChange={handleChange} required />
            <Input placeholder="Phone Number" name="phone" value={formData.phone} onChange={handleChange} required />
            <Input placeholder="Department" name="department" value={formData.department} onChange={handleChange} required />
            <Input placeholder="Designation" name="designation" value={formData.designation} onChange={handleChange} required />
            <Input placeholder="Current Station" name="currentStation" value={formData.currentStation} onChange={handleChange} required />
            <Input placeholder="Desired Station" name="desiredStation" value={formData.desiredStation} onChange={handleChange} required />
            <Button type="submit" className="w-full">Register</Button>
          </form>
        </CardContent>
      </Card>

      <Card className="w-full max-w-xl shadow-lg">
        <CardContent className="p-6">
          <h2 className="text-xl font-semibold mb-4 text-center text-green-700">Login</h2>
          <form onSubmit={handleLogin} className="space-y-4">
            <Input placeholder="Employee ID" name="loginId" required />
            <Input type="password" placeholder="Password" name="password" required />
            <Button type="submit" className="w-full">Login</Button>
          </form>
        </CardContent>
      </Card>
    </div>
  );
}
