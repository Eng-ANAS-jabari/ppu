    .score-input { 
        border: 2px solid #e2e8f0; 
        transition: all 0.2s; 
        text-align: center; 
        font-weight: 700; 
        font-size: 1.1rem; 
    }
    
    .score-input:focus { 
        border-color: #4f46e5; 
        outline: none; 
        background-color: #fffbeb; 
        box-shadow: 0 0 0 3px rgba(79, 70, 229, 0.1);
    }
    
    .student-card {
        transition: all 0.3s ease;
        border: 2px solid transparent;
    }
    
    .student-card:hover {
        transform: translateY(-5px);
        box-shadow: 0 20px 40px rgba(0, 0, 0, 0.1);
        border-color: #4f46e5;
    }
    
    @keyframes fadeIn {
        from { opacity: 0; transform: translateY(20px); }
        to { opacity: 1; transform: translateY(0); }
    }
    
    .fade-in {
        animation: fadeIn 0.5s ease forwards;
    }
    
    .whatsapp-modal {
        position: fixed;
        top: 0;
        left: 0;
        width: 100%;
        height: 100%;
        background: rgba(0, 0, 0, 0.7);
        display: none;
        justify-content: center;
        align-items: center;
        z-index: 1000;
    }
    
    .whatsapp-modal-content {
        background: white;
        padding: 30px;
        border-radius: 20px;
        width: 90%;
        max-width: 500px;
        max-height: 80vh;
        overflow-y: auto;
    }
    
    .admin-student-item {
        display: flex;
        justify-content: space-between;
        align-items: center;
        padding: 10px;
        margin: 5px 0;
        background: #f8fafc;
        border-radius: 10px;
        border: 1px solid #e2e8f0;
    }
    
    @media print { 
        .no-print { display: none !important; } 
        body { padding: 0 !important; background: white !important; } 
        .container { box-shadow: none !important; border: none !important; }
        .student-card { border: 1px solid #eee !important; break-inside: avoid; }
    }
</style>
