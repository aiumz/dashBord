# dashBord


/**
 * 设计思想
 * 1.将业务和canvas依赖抽离出来,元素render方法实现图形的绘制
 * 2.利用舞台管理所有的元素和连线
 * 3.利用自定义事件注册来实现各个元素间的配合 【“parent_move”,"start_move","end_move","create_link",...】
 * 
 */

export interface DashBordConfig{

}
export interface CanvasConfig{
    width:number;
    height:number;
}

export interface Stage{
    /**
     * 根据不同的canvas库来确定
     */
    canvasObj:any;
    constructor(dashBordConfig:DashBordConfig,canvasConfig:CanvasConfig);
    getCanvasObj():any;
    /**
     * 事件存储
     */
    events:{[key:string]:Function[]}
    /**
     * 注册全局事件
     * @param eventName 
     * @param callback 
     */
    on(eventName:string,callback:Function):void;
    /**
     * 触发全局事件
     * @param eventName 
     * @param data 
     */
    brodcast(eventName:string,data:any):void;
    /**
     * 取消事件注册
     * @param eventName 
     * @param callback 
     */
    off(eventName:string,callback?:Function);
    /**
     * 具体元素
     */
    elements: Element[];
    /**
     * 连线
     */
    Links:Link[];
    /**
     * 存储元素 首先给子元素stage赋值 并依次调用元素的 init render registerEvent 方法;
     * @param Element 
     */
    addElement(el:Element): void;
    /**
     * 存储线 首先给子元素stage赋值 并依次调用元素的 init render registerEvent 方法;
     */
    addLink(Link): void;
    /**
     * 根据id删除元素、线、分组  
     * @param id 
     */
    removeEL(id: string): Element | Link;
    /**
     * 当前显示在历史记录中的位置
     */
    historyIndex:number;
    /**
     * 历史记录
     */
    history: DashBordConfig[];
    /**
     * 添加历史记录
     * @param conf 
     */
    addHistory(conf:DashBordConfig):void;
    /**
     * 历史记录后退
     */
    historyGoPrev();
    /**
     * 历史记录前进
     */
    historyGoNext();
    /**
     * 获取当前的视图配置
     */
    getDashBordConfig(): DashBordConfig;
    /**
     * 刷新视图为当前传入的配置
     * @param conf 
     */
    setDashBordConfig(conf: DashBordConfig);
    /**
     * 绘制背景网格
     */
    render():void;
    /**
    * 注册自定义事件
    */
    registerEvent(): void;
    
}
/**
 * 具体元素集成此元素并实现 render   选择实现onMove onClick onEnterOther onLeaveOther onLinkToOther  onBreakLinkToother
 */
export interface Element{
    stage:Stage;
    constructor(elementConf)
    /**
     * 组件Id
     */
    id:string;
    /**
     * 组件名称
     */
    name:string;
    /**
     * 坐标X
     */
    x: number;
    /**
     *  坐标Y
     */
    y:number;
      /**
     * 依赖白名单
     */
    despWhiteList:string[];
    /**
     * 依赖
     */
    desps: string[];
    /**
     * 子元素白名单 
     */
    childrenWhiteList: string[];
    /**
     * 子元素Id
     */
    children: string[];
    /**
     * 渲染方法
     */
    render(): void;
    /**
     * 注册自定义事件
     */
    registerEvent(): void;
    /**
     * 初始化参数方法
     */
    init():void;
    /**
     * 当当前元素点击的时候
     */
    onClick():void;
    /**
     * 当拖拽时
     */
    onDrag():Promise<boolean>;
    /**
     * 当进入其他元素的时候
     */
    onEnterOther:Promise<boolean>;
    /**
     * 当离开其他元素的时候
     */
    onLeaveOther:Promise<boolean>;
    /**
     * 当连接到其他元素的时候
     */
    onLinkToOther:Promise<boolean>;
    /**
     * 当从其他元素断开连接的时候
     */
    onBreakLinkToother:Promise<boolean>;
}


/**
 * 链接线由元素的依赖分析得出
 */

export interface Link {
    constructor(LinkConf)
    stage: Stage;
    name: string;
    /**
     * 起点Id
     */
    fromId:string;
    /**
     * 终点Id
     */
    toId:string;
     /**
     * 渲染方法
     */
    render(): void;
    /**
     * 获取开始点的坐标
     */
    getStartPointer(fromId:string):{x:number;y:number};
    /**
     * 获取结束点的坐标
     */
    getEndPointer(toId?:string): { x: number; y: number };
    /**
    * 注册自定义事件
    */
    registerEvent(): void;
}


