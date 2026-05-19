import React, { useState, useEffect } from 'react';
import { Menu, X, User, LogIn, LogOut, Search, ChevronRight, Edit, Trash2, PlusCircle, Newspaper } from 'lucide-react';

// --- MOCK DATA ---
const initialNews = [
  { id: 1, title: "Global Tech Summit Announces New AI Breakthroughs", category: "Technology", content: "Major tech companies gathered today to announce significant advancements in artificial intelligence, promising to revolutionize how we interact with everyday devices.", date: "2026-05-16", author: "Admin", image: "https://placehold.co/600x400/2a2a2a/ffffff?text=Tech+Summit" },
  { id: 2, title: "Local Team Wins Championship After 20 Years", category: "Sports", content: "In a stunning upset, the city's home team secured the national championship title last night, ending a two-decade drought and sparking city-wide celebrations.", date: "2026-05-15", author: "Sports Desk", image: "https://placehold.co/600x400/1a365d/ffffff?text=Sports+Victory" },
  { id: 3, title: "New Healthy Eating Guidelines Released", category: "Lifestyle", content: "Health experts have published revised guidelines for daily nutritional intake, emphasizing whole foods and sustainable eating habits for long-term wellness.", date: "2026-05-14", author: "Health Editor", image: "https://placehold.co/600x400/276749/ffffff?text=Healthy+Eating" },
  { id: 4, title: "Blockbuster Movie Breaks Box Office Records", category: "Entertainment", content: "The highly anticipated sci-fi epic has shattered all previous opening weekend records, drawing massive crowds globally.", date: "2026-05-16", author: "Entertainment Weekly", image: "https://placehold.co/600x400/742a2a/ffffff?text=Blockbuster" },
  { id: 5, title: "City Council Approves New Public Park", category: "Local News", content: "The downtown area will soon see a massive new green space, as city officials greenlit the 'Urban Oasis' project aimed at improving local air quality.", date: "2026-05-13", author: "Local Reporter", image: "https://placehold.co/600x400/2f855a/ffffff?text=Public+Park" },
];

const mockUsers = [
  { id: 1, name: "Admin User", email: "admin@enews.com", password: "password", role: "admin", city: "Delhi", contact: "1234567890" },
  { id: 2, name: "Normal User", email: "user@enews.com", password: "password", role: "user", city: "Mumbai", contact: "0987654321" },
];

// --- MAIN APP COMPONENT ---
export default function App() {
  const [currentView, setCurrentView] = useState('home');
  const [currentUser, setCurrentUser] = useState(null);
  const [articles, setArticles] = useState(initialNews);
  const [users, setUsers] = useState(mockUsers);
  const [selectedArticle, setSelectedArticle] = useState(null);
  const [activeCategory, setActiveCategory] = useState('All');
  const [isMobileMenuOpen, setIsMobileMenuOpen] = useState(false);
  const [searchQuery, setSearchQuery] = useState('');

  // Navigation Helper
  const navigateTo = (view, data = null) => {
    setCurrentView(view);
    if (data) setSelectedArticle(data);
    setIsMobileMenuOpen(false);
    window.scrollTo(0, 0);
  };

  // Auth Helpers
  const handleLogin = (email, password) => {
    const user = users.find(u => u.email === email && u.password === password);
    if (user) {
      setCurrentUser(user);
      navigateTo('home');
      return true;
    }
    return false;
  };

  const handleRegister = (newUser) => {
    setUsers([...users, { ...newUser, id: users.length + 1, role: 'user' }]);
    setCurrentUser({ ...newUser, id: users.length + 1, role: 'user' });
    navigateTo('home');
  };

  const handleLogout = () => {
    setCurrentUser(null);
    navigateTo('home');
  };

  // --- SUB-COMPONENTS ---

  const Navbar = () => (
    <header className="bg-slate-900 text-white sticky top-0 z-50">
      <div className="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
        <div className="flex justify-between h-16 items-center">
          {/* Logo */}
          <div className="flex items-center cursor-pointer" onClick={() => { setActiveCategory('All'); navigateTo('home'); }}>
            <Newspaper className="h-8 w-8 mr-2 text-blue-400" />
            <span className="font-serif text-2xl font-bold tracking-wider">E-News Paper</span>
          </div>

          {/* Desktop Navigation */}
          <nav className="hidden md:flex space-x-6">
            {['All', 'Technology', 'Sports', 'Lifestyle', 'Entertainment', 'Local News'].map(cat => (
              <button 
                key={cat} 
                onClick={() => { setActiveCategory(cat); navigateTo('home'); }}
                className={`hover:text-blue-400 transition-colors ${activeCategory === cat && currentView === 'home' ? 'text-blue-400 font-semibold border-b-2 border-blue-400' : 'text-gray-300'}`}
              >
                {cat}
              </button>
            ))}
          </nav>

          {/* User Actions */}
          <div className="hidden md:flex items-center space-x-4">
            <div className="relative">
              <input 
                type="text" 
                placeholder="Search news..." 
                className="bg-slate-800 text-white rounded-full py-1 px-4 pl-10 focus:outline-none focus:ring-2 focus:ring-blue-400 text-sm"
                value={searchQuery}
                onChange={(e) => setSearchQuery(e.target.value)}
              />
              <Search className="h-4 w-4 absolute left-3 top-2 text-gray-400" />
            </div>
            
            {currentUser ? (
              <div className="flex items-center space-x-4">
                <span className="text-sm text-gray-300">Hi, {currentUser.name}</span>
                {currentUser.role === 'admin' && (
                  <button onClick={() => navigateTo('admin')} className="text-sm bg-blue-600 hover:bg-blue-700 px-3 py-1 rounded transition">Dashboard</button>
                )}
                <button onClick={handleLogout} title="Logout" className="text-gray-300 hover:text-white"><LogOut className="h-5 w-5" /></button>
              </div>
            ) : (
              <div className="space-x-2">
                <button onClick={() => navigateTo('login')} className="text-sm hover:text-blue-400 transition">Log In</button>
                <button onClick={() => navigateTo('register')} className="text-sm bg-blue-600 hover:bg-blue-700 px-4 py-1.5 rounded-full transition">Register</button>
              </div>
            )}
          </div>

          {/* Mobile Menu Button */}
          <div className="md:hidden flex items-center">
            <button onClick={() => setIsMobileMenuOpen(!isMobileMenuOpen)} className="text-gray-300 hover:text-white">
              {isMobileMenuOpen ? <X className="h-6 w-6" /> : <Menu className="h-6 w-6" />}
            </button>
          </div>
        </div>
      </div>

      {/* Mobile Navigation Menu */}
      {isMobileMenuOpen && (
        <div className="md:hidden bg-slate-800 px-2 pt-2 pb-3 space-y-1">
          {['All', 'Technology', 'Sports', 'Lifestyle', 'Entertainment', 'Local News'].map(cat => (
             <button 
                key={cat} 
                onClick={() => { setActiveCategory(cat); navigateTo('home'); }}
                className="block w-full text-left px-3 py-2 text-base font-medium text-gray-300 hover:text-white hover:bg-slate-700 rounded-md"
              >
                {cat}
              </button>
          ))}
          <div className="border-t border-slate-700 pt-4 pb-2">
            {currentUser ? (
              <>
                <div className="px-3 text-sm text-gray-400 mb-2">Logged in as {currentUser.name}</div>
                {currentUser.role === 'admin' && (
                  <button onClick={() => navigateTo('admin')} className="block w-full text-left px-3 py-2 text-base font-medium text-blue-400 hover:bg-slate-700 rounded-md">Admin Dashboard</button>
                )}
                <button onClick={handleLogout} className="block w-full text-left px-3 py-2 text-base font-medium text-red-400 hover:bg-slate-700 rounded-md">Log Out</button>
              </>
            ) : (
              <>
                <button onClick={() => navigateTo('login')} className="block w-full text-left px-3 py-2 text-base font-medium text-gray-300 hover:text-white hover:bg-slate-700 rounded-md">Log In</button>
                <button onClick={() => navigateTo('register')} className="block w-full text-left px-3 py-2 text-base font-medium text-blue-400 hover:bg-slate-700 rounded-md">Register</button>
              </>
            )}
          </div>
        </div>
      )}
    </header>
  );

  const HomeView = () => {
    let filtered = activeCategory === 'All' ? articles : articles.filter(a => a.category === activeCategory);
    if (searchQuery) {
      filtered = filtered.filter(a => a.title.toLowerCase().includes(searchQuery.toLowerCase()) || a.content.toLowerCase().includes(searchQuery.toLowerCase()));
    }

    const featuredArticle = filtered.length > 0 ? filtered[0] : null;
    const remainingArticles = filtered.slice(1);

    return (
      <div className="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-8">
        <div className="border-b-4 border-slate-900 mb-8 pb-2 flex justify-between items-end">
          <h1 className="text-3xl font-serif font-bold uppercase tracking-widest text-slate-900">{activeCategory === 'All' ? 'Top Headlines' : activeCategory}</h1>
          <span className="text-gray-500 font-medium text-sm">{new Date().toLocaleDateString('en-US', { weekday: 'long', year: 'numeric', month: 'long', day: 'numeric' })}</span>
        </div>

        {filtered.length === 0 ? (
          <div className="text-center py-20 text-gray-500">No articles found in this category.</div>
        ) : (
          <div className="grid grid-cols-1 lg:grid-cols-3 gap-8">
            {/* Featured Article - Spans 2 columns on large screens */}
            {featuredArticle && (
              <div className="lg:col-span-2 cursor-pointer group" onClick={() => navigateTo('article', featuredArticle)}>
                <div className="overflow-hidden rounded-lg shadow-md mb-4 relative h-96">
                  <img src={featuredArticle.image} alt={featuredArticle.title} className="w-full h-full object-cover transition duration-500 group-hover:scale-105" />
                  <div className="absolute top-4 left-4 bg-blue-600 text-white text-xs font-bold px-3 py-1 uppercase rounded-full shadow">{featuredArticle.category}</div>
                </div>
                <h2 className="text-4xl font-serif font-bold mb-3 group-hover:text-blue-700 transition-colors leading-tight">{featuredArticle.title}</h2>
                <p className="text-gray-600 text-lg mb-4 line-clamp-3">{featuredArticle.content}</p>
                <div className="flex items-center text-sm text-gray-500 font-medium">
                  <span>By {featuredArticle.author}</span>
                  <span className="mx-2">•</span>
                  <span>{featuredArticle.date}</span>
                </div>
              </div>
            )}

            {/* Sidebar Articles */}
            <div className="flex flex-col space-y-6">
              {remainingArticles.slice(0, 3).map(article => (
                <div key={article.id} className="cursor-pointer group flex flex-col sm:flex-row lg:flex-col gap-4 border-b pb-6 last:border-0" onClick={() => navigateTo('article', article)}>
                  <div className="w-full sm:w-1/3 lg:w-full h-40 lg:h-48 overflow-hidden rounded-lg shrink-0 relative">
                     <img src={article.image} alt={article.title} className="w-full h-full object-cover transition duration-300 group-hover:scale-105" />
                  </div>
                  <div>
                    <span className="text-blue-600 text-xs font-bold uppercase tracking-wider">{article.category}</span>
                    <h3 className="text-xl font-serif font-bold mt-1 mb-2 group-hover:text-blue-700 transition-colors leading-snug">{article.title}</h3>
                    <div className="text-xs text-gray-500">{article.date}</div>
                  </div>
                </div>
              ))}
            </div>
          </div>
        )}
        
        {/* Secondary Grid for remaining articles */}
        {remainingArticles.length > 3 && (
          <div className="mt-12 pt-8 border-t-2 border-gray-200">
            <h3 className="text-2xl font-serif font-bold mb-6">More from {activeCategory}</h3>
            <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-6">
              {remainingArticles.slice(3).map(article => (
                <div key={article.id} className="cursor-pointer group" onClick={() => navigateTo('article', article)}>
                   <div className="h-40 overflow-hidden rounded-lg mb-3">
                     <img src={article.image} alt={article.title} className="w-full h-full object-cover transition duration-300 group-hover:scale-105" />
                  </div>
                  <h4 className="font-serif font-bold text-lg group-hover:text-blue-700 line-clamp-2">{article.title}</h4>
                  <div className="text-xs text-gray-500 mt-2">{article.date}</div>
                </div>
              ))}
            </div>
          </div>
        )}
      </div>
    );
  };

  const ArticleView = () => {
    if (!selectedArticle) return null;
    return (
      <div className="max-w-4xl mx-auto px-4 sm:px-6 lg:px-8 py-10">
        <button onClick={() => navigateTo('home')} className="text-blue-600 font-medium hover:underline mb-6 flex items-center">
          &larr; Back to {activeCategory}
        </button>
        <div className="mb-6">
          <span className="bg-slate-100 text-slate-800 text-sm font-bold px-3 py-1 rounded uppercase tracking-wider">{selectedArticle.category}</span>
        </div>
        <h1 className="text-4xl md:text-5xl font-serif font-bold mb-6 leading-tight">{selectedArticle.title}</h1>
        
        <div className="flex items-center text-gray-600 mb-8 border-y py-4 border-gray-200">
          <div className="h-10 w-10 bg-slate-300 rounded-full flex items-center justify-center mr-3 text-slate-600">
            <User className="h-6 w-6" />
          </div>
          <div>
            <div className="font-bold text-slate-900">{selectedArticle.author}</div>
            <div className="text-sm">{selectedArticle.date}</div>
          </div>
        </div>

        <div className="w-full h-64 md:h-96 rounded-xl overflow-hidden mb-10 shadow-lg">
          <img src={selectedArticle.image} alt={selectedArticle.title} className="w-full h-full object-cover" />
        </div>

        <div className="prose prose-lg max-w-none font-serif text-gray-800 leading-relaxed space-y-6">
           {/* Replicating content to simulate a longer article */}
          <p className="text-xl leading-relaxed first-letter:text-5xl first-letter:font-bold first-letter:float-left first-letter:mr-3 first-letter:mt-1">{selectedArticle.content}</p>
          <p>This development comes as a surprise to many industry analysts who had predicted a slower pace of innovation in this sector. However, the coordinated efforts revealed today suggest a highly motivated and accelerated timeline.</p>
          <p>Looking ahead, the implications of this news are vast. From shifting market dynamics to everyday consumer impacts, the ripple effects will likely be felt for months, if not years, to come. Stakeholders are advised to monitor the situation closely as more details continue to emerge.</p>
        </div>

        {/* Comment Section Simulation based on Project Report requirements */}
        <div className="mt-16 pt-8 border-t border-gray-200">
          <h3 className="text-2xl font-bold mb-6">Discussion</h3>
          {!currentUser ? (
            <div className="bg-blue-50 border border-blue-200 text-blue-800 p-4 rounded-lg flex justify-between items-center">
              <span>Please log in to participate in the discussion.</span>
              <button onClick={() => navigateTo('login')} className="bg-blue-600 text-white px-4 py-2 rounded shadow hover:bg-blue-700">Log In</button>
            </div>
          ) : (
             <div className="bg-white border rounded-lg p-4 shadow-sm">
               <textarea className="w-full border-gray-300 rounded p-3 focus:ring-blue-500 focus:border-blue-500 bg-gray-50 border" rows="3" placeholder="Add a comment..."></textarea>
               <div className="mt-3 flex justify-end">
                 <button className="bg-slate-900 text-white px-6 py-2 rounded font-medium hover:bg-slate-800 transition">Post Comment</button>
               </div>
             </div>
          )}
        </div>
      </div>
    );
  };

  const AuthView = ({ isLogin }) => {
    const [formData, setFormData] = useState({ name: '', email: '', password: '', contact: '', city: '' });
    const [error, setError] = useState('');

    const handleSubmit = (e) => {
      e.preventDefault();
      if (isLogin) {
        if (!handleLogin(formData.email, formData.password)) {
          setError('Invalid Email id or Password');
        }
      } else {
        if (!formData.name || !formData.email || !formData.password) {
           setError('Please fill in all required fields.');
           return;
        }
        handleRegister(formData);
      }
    };

    return (
      <div className="min-h-[80vh] flex items-center justify-center bg-gray-50 py-12 px-4 sm:px-6 lg:px-8">
        <div className="max-w-md w-full space-y-8 bg-white p-10 rounded-xl shadow-xl border border-gray-100">
          <div>
            <h2 className="text-center text-3xl font-extrabold text-gray-900 font-serif">
              {isLogin ? 'Welcome Back' : 'Create an Account'}
            </h2>
            <p className="mt-2 text-center text-sm text-gray-600">
              {isLogin ? 'Enter your details to access your account' : 'Join E-News Paper today'}
            </p>
          </div>
          <form className="mt-8 space-y-6" onSubmit={handleSubmit}>
            {error && <div className="bg-red-50 text-red-500 p-3 rounded text-sm text-center border border-red-200">{error}</div>}
            
            <div className="rounded-md shadow-sm space-y-4">
              {!isLogin && (
                <div>
                  <label className="block text-sm font-medium text-gray-700">Full Name *</label>
                  <input type="text" required className="mt-1 appearance-none relative block w-full px-3 py-2 border border-gray-300 placeholder-gray-500 text-gray-900 rounded focus:outline-none focus:ring-blue-500 focus:border-blue-500 focus:z-10 sm:text-sm" placeholder="John Doe" onChange={e => setFormData({...formData, name: e.target.value})} />
                </div>
              )}
              <div>
                <label className="block text-sm font-medium text-gray-700">Email address *</label>
                <input type="email" required className="mt-1 appearance-none relative block w-full px-3 py-2 border border-gray-300 placeholder-gray-500 text-gray-900 rounded focus:outline-none focus:ring-blue-500 focus:border-blue-500 focus:z-10 sm:text-sm" placeholder="user@example.com" onChange={e => setFormData({...formData, email: e.target.value})} />
              </div>
              <div>
                <label className="block text-sm font-medium text-gray-700">Password *</label>
                <input type="password" required className="mt-1 appearance-none relative block w-full px-3 py-2 border border-gray-300 placeholder-gray-500 text-gray-900 rounded focus:outline-none focus:ring-blue-500 focus:border-blue-500 focus:z-10 sm:text-sm" placeholder="••••••••" onChange={e => setFormData({...formData, password: e.target.value})} />
              </div>
              
              {!isLogin && (
                <>
                  <div>
                    <label className="block text-sm font-medium text-gray-700">City</label>
                    <input type="text" className="mt-1 appearance-none relative block w-full px-3 py-2 border border-gray-300 placeholder-gray-500 text-gray-900 rounded focus:outline-none focus:ring-blue-500 focus:border-blue-500 sm:text-sm" placeholder="Delhi" onChange={e => setFormData({...formData, city: e.target.value})} />
                  </div>
                  <div>
                    <label className="block text-sm font-medium text-gray-700">Contact Number</label>
                    <input type="tel" className="mt-1 appearance-none relative block w-full px-3 py-2 border border-gray-300 placeholder-gray-500 text-gray-900 rounded focus:outline-none focus:ring-blue-500 focus:border-blue-500 sm:text-sm" placeholder="1234567890" onChange={e => setFormData({...formData, contact: e.target.value})} />
                  </div>
                </>
              )}
            </div>

            <div>
              <button type="submit" className="group relative w-full flex justify-center py-3 px-4 border border-transparent text-sm font-medium rounded-md text-white bg-slate-900 hover:bg-slate-800 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-slate-900 transition shadow-lg">
                <span className="absolute left-0 inset-y-0 flex items-center pl-3">
                  <LogIn className="h-5 w-5 text-slate-500 group-hover:text-slate-400" aria-hidden="true" />
                </span>
                {isLogin ? 'Sign In' : 'Register Now'}
              </button>
            </div>
            
            <div className="text-center text-sm">
               {isLogin ? (
                 <p>Don't have an account? <button type="button" onClick={() => navigateTo('register')} className="font-medium text-blue-600 hover:text-blue-500">Register here</button></p>
               ) : (
                 <p>Already have an account? <button type="button" onClick={() => navigateTo('login')} className="font-medium text-blue-600 hover:text-blue-500">Log in</button></p>
               )}
            </div>
          </form>
        </div>
      </div>
    );
  };

  const AdminView = () => {
    const [adminTab, setAdminTab] = useState('news'); // 'news' or 'users'

    if (!currentUser || currentUser.role !== 'admin') {
      return <div className="text-center py-20">Access Denied. Administrator privileges required.</div>;
    }

    const deleteArticle = (id) => {
      if(window.confirm('Are you sure you want to delete this article?')) {
        setArticles(articles.filter(a => a.id !== id));
      }
    };

    return (
      <div className="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-8">
        <h1 className="text-3xl font-bold text-slate-900 mb-8 font-serif border-b-2 pb-4">Administrator Dashboard</h1>
        
        <div className="flex space-x-4 mb-6 border-b border-gray-200">
          <button 
            className={`py-2 px-4 font-medium transition-colors ${adminTab === 'news' ? 'border-b-2 border-blue-600 text-blue-600' : 'text-gray-500 hover:text-gray-800'}`}
            onClick={() => setAdminTab('news')}
          >
            Manage News
          </button>
          <button 
            className={`py-2 px-4 font-medium transition-colors ${adminTab === 'users' ? 'border-b-2 border-blue-600 text-blue-600' : 'text-gray-500 hover:text-gray-800'}`}
            onClick={() => setAdminTab('users')}
          >
            Manage Users
          </button>
        </div>

        {adminTab === 'news' && (
          <div>
            <div className="flex justify-between items-center mb-4">
              <h2 className="text-xl font-bold text-gray-800">Published Articles</h2>
              <button className="flex items-center space-x-1 bg-green-600 text-white px-4 py-2 rounded hover:bg-green-700 transition">
                <PlusCircle className="w-4 h-4" /> <span>Add New Article</span>
              </button>
            </div>
            <div className="bg-white shadow overflow-hidden sm:rounded-md border border-gray-200">
              <ul className="divide-y divide-gray-200">
                {articles.map(article => (
                  <li key={article.id}>
                    <div className="px-4 py-4 sm:px-6 flex items-center justify-between hover:bg-gray-50">
                      <div className="flex-1 min-w-0 pr-4">
                        <p className="text-sm font-bold text-blue-600 truncate">{article.title}</p>
                        <p className="flex items-center text-sm text-gray-500 mt-1">
                          <span className="truncate">{article.category} • {article.date}</span>
                        </p>
                      </div>
                      <div className="flex space-x-2 shrink-0">
                        <button className="p-2 text-gray-400 hover:text-blue-600 transition"><Edit className="w-5 h-5" /></button>
                        <button onClick={() => deleteArticle(article.id)} className="p-2 text-gray-400 hover:text-red-600 transition"><Trash2 className="w-5 h-5" /></button>
                      </div>
                    </div>
                  </li>
                ))}
              </ul>
            </div>
          </div>
        )}

        {adminTab === 'users' && (
          <div>
            <h2 className="text-xl font-bold text-gray-800 mb-4">Registered Users</h2>
            <div className="bg-white shadow overflow-hidden sm:rounded-lg border border-gray-200">
              <table className="min-w-full divide-y divide-gray-200">
                <thead className="bg-gray-50">
                  <tr>
                    <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Name</th>
                    <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Email</th>
                    <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Role</th>
                    <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">City</th>
                  </tr>
                </thead>
                <tbody className="bg-white divide-y divide-gray-200">
                  {users.map(user => (
                    <tr key={user.id}>
                      <td className="px-6 py-4 whitespace-nowrap text-sm font-medium text-gray-900">{user.name}</td>
                      <td className="px-6 py-4 whitespace-nowrap text-sm text-gray-500">{user.email}</td>
                      <td className="px-6 py-4 whitespace-nowrap text-sm text-gray-500">
                        <span className={`px-2 inline-flex text-xs leading-5 font-semibold rounded-full ${user.role === 'admin' ? 'bg-purple-100 text-purple-800' : 'bg-green-100 text-green-800'}`}>
                          {user.role}
                        </span>
                      </td>
                      <td className="px-6 py-4 whitespace-nowrap text-sm text-gray-500">{user.city || 'N/A'}</td>
                    </tr>
                  ))}
                </tbody>
              </table>
            </div>
          </div>
        )}
      </div>
    );
  };

  const Footer = () => (
    <footer className="bg-slate-900 text-slate-400 py-8 mt-12 border-t-4 border-slate-800">
      <div className="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 flex flex-col md:flex-row justify-between items-center">
        <div className="flex items-center mb-4 md:mb-0">
          <Newspaper className="h-6 w-6 mr-2 text-slate-500" />
          <span className="font-serif text-xl font-bold text-white tracking-wider">E-News Paper</span>
        </div>
        <div className="text-sm text-center md:text-left">
          &copy; {new Date().getFullYear()} E-News Paper Portal. All rights reserved. <br/>
          <span className="text-xs mt-1 block">Project Report Implementation</span>
        </div>
      </div>
    </footer>
  );

  // --- RENDER ROUTER ---
  return (
    <div className="min-h-screen flex flex-col bg-slate-50 font-sans">
      <Navbar />
      <main className="flex-grow">
        {currentView === 'home' && <HomeView />}
        {currentView === 'article' && <ArticleView />}
        {currentView === 'login' && <AuthView isLogin={true} />}
        {currentView === 'register' && <AuthView isLogin={false} />}
        {currentView === 'admin' && <AdminView />}
      </main>
      <Footer />
    </div>
  );
}
