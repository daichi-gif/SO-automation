# SO-automation
import React, { useState } from 'react';
import { BarChart, Bar, XAxis, YAxis, CartesianGrid, Tooltip, ResponsiveContainer, LineChart, Line, PieChart, Pie, Cell } from 'recharts';
import { Users, TrendingUp, Award, Calendar, Plus, Eye, AlertCircle } from 'lucide-react';

const StockOptionManager = () => {
  const [activeTab, setActiveTab] = useState('dashboard');
  
  const [employees, setEmployees] = useState([
    {
      id: 1,
      name: '田中太郎',
      department: 'エンジニアリング',
      joinDate: '2022-04-01',
      totalGranted: 1000,
      vested: 250,
      exercised: 0,
      unvested: 750,
      strikePrice: 1000,
      currentPrice: 2500
    },
    {
      id: 2,
      name: '佐藤花子',
      department: 'セールス',
      joinDate: '2021-01-15',
      totalGranted: 1500,
      vested: 1125,
      exercised: 500,
      unvested: 375,
      strikePrice: 800,
      currentPrice: 2500
    },
    {
      id: 3,
      name: '鈴木一郎',
      department: 'マーケティング',
      joinDate: '2023-07-01',
      totalGranted: 800,
      vested: 100,
      exercised: 0,
      unvested: 700,
      strikePrice: 1200,
      currentPrice: 2500
    }
  ]);

  const [grants, setGrants] = useState([
    { id: 1, employeeId: 1, grantDate: '2022-04-01', shares: 1000, strikePrice: 1000, vestingSchedule: '4年間・25%ずつ' },
    { id: 2, employeeId: 2, grantDate: '2021-01-15', shares: 1500, strikePrice: 800, vestingSchedule: '4年間・25%ずつ' },
    { id: 3, employeeId: 3, grantDate: '2023-07-01', shares: 800, strikePrice: 1200, vestingSchedule: '4年間・25%ずつ' }
  ]);

  const [showModal, setShowModal] = useState(false);
  const [modalType, setModalType] = useState('grant');

  // ダッシュボード用データ
  const vestingProgress = [
    { month: '2024-01', vested: 800, exercised: 200 },
    { month: '2024-02', vested: 950, exercised: 250 },
    { month: '2024-03', vested: 1100, exercised: 300 },
    { month: '2024-04', vested: 1250, exercised: 400 },
    { month: '2024-05', vested: 1400, exercised: 450 },
    { month: '2024-06', vested: 1475, exercised: 500 },
    { month: '2024-07', vested: 1475, exercised: 500 }
  ];

  const departmentData = [
    { name: 'エンジニアリング', value: 45, color: '#8884d8' },
    { name: 'セールス', value: 30, color: '#82ca9d' },
    { name: 'マーケティング', value: 15, color: '#ffc658' },
    { name: 'その他', value: 10, color: '#ff7c7c' }
  ];

  const calculateUnrealizedGain = (emp) => {
    return (emp.currentPrice - emp.strikePrice) * emp.vested;
  };

  const totalStats = {
    totalEmployees: employees.length,
    totalGranted: employees.reduce((sum, emp) => sum + emp.totalGranted, 0),
    totalVested: employees.reduce((sum, emp) => sum + emp.vested, 0),
    totalExercised: employees.reduce((sum, emp) => sum + emp.exercised, 0),
    totalUnrealizedGain: employees.reduce((sum, emp) => sum + calculateUnrealizedGain(emp), 0)
  };

  const handleExercise = (employeeId, shares) => {
    setEmployees(employees.map(emp => 
      emp.id === employeeId 
        ? { ...emp, exercised: emp.exercised + shares, vested: emp.vested - shares }
        : emp
    ));
  };

  const renderDashboard = () => (
    <div className="space-y-6">
      {/* 統計カード */}
      <div className="grid grid-cols-1 md:grid-cols-4 gap-6">
        <div className="bg-white p-6 rounded-lg shadow-md">
          <div className="flex items-center justify-between">
            <div>
              <p className="text-gray-500 text-sm">対象従業員数</p>
              <p className="text-2xl font-bold">{totalStats.totalEmployees}</p>
            </div>
            <Users className="h-8 w-8 text-blue-500" />
          </div>
        </div>
        
        <div className="bg-white p-6 rounded-lg shadow-md">
          <div className="flex items-center justify-between">
            <div>
              <p className="text-gray-500 text-sm">総付与株数</p>
              <p className="text-2xl font-bold">{totalStats.totalGranted.toLocaleString()}</p>
            </div>
            <Award className="h-8 w-8 text-green-500" />
          </div>
        </div>

        <div className="bg-white p-6 rounded-lg shadow-md">
          <div className="flex items-center justify-between">
            <div>
              <p className="text-gray-500 text-sm">行使済株数</p>
              <p className="text-2xl font-bold">{totalStats.totalExercised.toLocaleString()}</p>
            </div>
            <TrendingUp className="h-8 w-8 text-purple-500" />
          </div>
        </div>

        <div className="bg-white p-6 rounded-lg shadow-md">
          <div className="flex items-center justify-between">
            <div>
              <p className="text-gray-500 text-sm">含み益総額</p>
              <p className="text-2xl font-bold">¥{(totalStats.totalUnrealizedGain / 1000000).toFixed(1)}M</p>
            </div>
            <TrendingUp className="h-8 w-8 text-orange-500" />
          </div>
        </div>
      </div>

      {/* チャート */}
      <div className="grid grid-cols-1 lg:grid-cols-2 gap-6">
        <div className="bg-white p-6 rounded-lg shadow-md">
          <h3 className="text-lg font-semibold mb-4">ベスティング推移</h3>
          <ResponsiveContainer width="100%" height={300}>
            <LineChart data={vestingProgress}>
              <CartesianGrid strokeDasharray="3 3" />
              <XAxis dataKey="month" />
              <YAxis />
              <Tooltip />
              <Line type="monotone" dataKey="vested" stroke="#8884d8" strokeWidth={2} name="ベスト済" />
              <Line type="monotone" dataKey="exercised" stroke="#82ca9d" strokeWidth={2} name="行使済" />
            </LineChart>
          </ResponsiveContainer>
        </div>

        <div className="bg-white p-6 rounded-lg shadow-md">
          <h3 className="text-lg font-semibold mb-4">部門別分布</h3>
          <ResponsiveContainer width="100%" height={300}>
            <PieChart>
              <Pie
                data={departmentData}
                cx="50%"
                cy="50%"
                labelLine={false}
                label={({ name, percent }) => `${name} ${(percent * 100).toFixed(0)}%`}
                outerRadius={80}
                fill="#8884d8"
                dataKey="value"
              >
                {departmentData.map((entry, index) => (
                  <Cell key={`cell-${index}`} fill={entry.color} />
                ))}
              </Pie>
              <Tooltip />
            </PieChart>
          </ResponsiveContainer>
        </div>
      </div>
    </div>
  );

  const renderEmployees = () => (
    <div className="space-y-6">
      <div className="flex justify-between items-center">
        <h2 className="text-2xl font-bold">従業員管理</h2>
        <button 
          onClick={() => { setModalType('grant'); setShowModal(true); }}
          className="bg-blue-500 text-white px-4 py-2 rounded-lg flex items-center gap-2 hover:bg-blue-600"
        >
          <Plus className="h-4 w-4" />
          新規付与
        </button>
      </div>

      <div className="bg-white rounded-lg shadow-md overflow-hidden">
        <div className="overflow-x-auto">
          <table className="min-w-full">
            <thead className="bg-gray-50">
              <tr>
                <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase">従業員名</th>
                <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase">部署</th>
                <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase">総付与</th>
                <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase">ベスト済</th>
                <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase">行使済</th>
                <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase">含み益</th>
                <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase">アクション</th>
              </tr>
            </thead>
            <tbody className="bg-white divide-y divide-gray-200">
              {employees.map((emp) => (
                <tr key={emp.id}>
                  <td className="px-6 py-4 whitespace-nowrap font-medium">{emp.name}</td>
                  <td className="px-6 py-4 whitespace-nowrap text-gray-500">{emp.department}</td>
                  <td className="px-6 py-4 whitespace-nowrap">{emp.totalGranted.toLocaleString()}</td>
                  <td className="px-6 py-4 whitespace-nowrap text-green-600">{emp.vested.toLocaleString()}</td>
                  <td className="px-6 py-4 whitespace-nowrap text-blue-600">{emp.exercised.toLocaleString()}</td>
                  <td className="px-6 py-4 whitespace-nowrap text-orange-600">
                    ¥{calculateUnrealizedGain(emp).toLocaleString()}
                  </td>
                  <td className="px-6 py-4 whitespace-nowrap">
                    <button
                      onClick={() => handleExercise(emp.id, Math.min(100, emp.vested))}
                      disabled={emp.vested === 0}
                      className="text-blue-600 hover:text-blue-900 disabled:text-gray-400 mr-3"
                    >
                      行使
                    </button>
                    <button className="text-gray-600 hover:text-gray-900">
                      <Eye className="h-4 w-4" />
                    </button>
                  </td>
                </tr>
              ))}
            </tbody>
          </table>
        </div>
      </div>
    </div>
  );

  const renderGrants = () => (
    <div className="space-y-6">
      <div className="flex justify-between items-center">
        <h2 className="text-2xl font-bold">付与履歴</h2>
        <button 
          onClick={() => { setModalType('grant'); setShowModal(true); }}
          className="bg-blue-500 text-white px-4 py-2 rounded-lg flex items-center gap-2 hover:bg-blue-600"
        >
          <Plus className="h-4 w-4" />
          新規付与
        </button>
      </div>

      <div className="bg-white rounded-lg shadow-md overflow-hidden">
        <div className="overflow-x-auto">
          <table className="min-w-full">
            <thead className="bg-gray-50">
              <tr>
                <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase">付与日</th>
                <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase">従業員</th>
                <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase">株数</th>
                <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase">行使価格</th>
                <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase">ベスティング</th>
                <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase">ステータス</th>
              </tr>
            </thead>
            <tbody className="bg-white divide-y divide-gray-200">
              {grants.map((grant) => {
                const employee = employees.find(emp => emp.id === grant.employeeId);
                return (
                  <tr key={grant.id}>
                    <td className="px-6 py-4 whitespace-nowrap">{grant.grantDate}</td>
                    <td className="px-6 py-4 whitespace-nowrap font-medium">{employee?.name}</td>
                    <td className="px-6 py-4 whitespace-nowrap">{grant.shares.toLocaleString()}</td>
                    <td className="px-6 py-4 whitespace-nowrap">¥{grant.strikePrice.toLocaleString()}</td>
                    <td className="px-6 py-4 whitespace-nowrap">{grant.vestingSchedule}</td>
                    <td className="px-6 py-4 whitespace-nowrap">
                      <span className="px-2 py-1 text-xs font-medium bg-blue-100 text-blue-800 rounded">
                        進行中
                      </span>
                    </td>
                  </tr>
                );
              })}
            </tbody>
          </table>
        </div>
      </div>
    </div>
  );

  return (
    <div className="min-h-screen bg-gray-50">
      {/* ヘッダー */}
      <header className="bg-white shadow-sm border-b">
        <div className="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
          <div className="flex justify-between items-center h-16">
            <h1 className="text-xl font-bold text-gray-900">ストックオプション管理システム</h1>
            <div className="flex items-center space-x-4">
              <span className="text-sm text-gray-500">現在株価: ¥2,500</span>
              <div className="h-2 w-2 bg-green-500 rounded-full"></div>
            </div>
          </div>
        </div>
      </header>

      {/* ナビゲーション */}
      <nav className="bg-white shadow-sm">
        <div className="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
          <div className="flex space-x-8">
            {[
              { key: 'dashboard', label: 'ダッシュボード' },
              { key: 'employees', label: '従業員管理' },
              { key: 'grants', label: '付与履歴' }
            ].map((tab) => (
              <button
                key={tab.key}
                onClick={() => setActiveTab(tab.key)}
                className={`py-4 px-1 border-b-2 font-medium text-sm ${
                  activeTab === tab.key
                    ? 'border-blue-500 text-blue-600'
                    : 'border-transparent text-gray-500 hover:text-gray-700'
                }`}
              >
                {tab.label}
              </button>
            ))}
          </div>
        </div>
      </nav>

      {/* メインコンテンツ */}
      <main className="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-8">
        {activeTab === 'dashboard' && renderDashboard()}
        {activeTab === 'employees' && renderEmployees()}
        {activeTab === 'grants' && renderGrants()}
      </main>

      {/* アラート */}
      <div className="fixed bottom-4 right-4 bg-blue-500 text-white p-4 rounded-lg shadow-lg max-w-sm">
        <div className="flex items-center gap-2">
          <AlertCircle className="h-5 w-5" />
          <div>
            <p className="font-medium">ベスティング通知</p>
            <p className="text-sm opacity-90">来月、3名の従業員のオプションがベストされます</p>
          </div>
        </div>
      </div>
    </div>
  );
};

export default StockOptionManager;
