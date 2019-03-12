# Требования к React компонентам

Мы используем стайлгайд от [Airbnb](https://github.com/airbnb/javascript/tree/master/react) с некоторыми дополнениями.

### Именование файлов

* Папка компонента - название компонента.
* Файлы компонента (jsx и стили) - название копонента.
* Папка с компонентом должна содержать package.json с указанием главного файла.

```
SomeComponent
  SomeComponent.jsx
  SomeComponent.styl
  package.json
```

**package.json**
```json
{
  "main": "AudioRecordButton.jsx"
}
```

----

### JS/JSX

Отступ: 4

#### Пример компонента-класса
```javascript
import React, { Component } from 'react'; // Блок импорта сторонних библиотек
import PropTypes from 'prop-types'; // Строки с одинарными кавычками
import cx from 'classnames';
// Строка отступа
import './ActionRow.styl'; // Стили
// Строка отступа
import { // Блок импорта внутренних компонентов
    PopupInfo, // Деструктуризация с более чем одним элементом - в несколько строк
    Heading,
    InfoIcon, // trailing coma
} from '../..';
// 2 строки отступа
// ---//---
/**
 * Компонент добавления/удаления бонусов, купонов и промо-кодов в корзине
 */
class ActionRow extends Component { // Класс по es6
    static displayName = 'ActionRow'; // Класс блока компонента и displayName по названию компонента
    // Одна строка отступа между статическими или прототипными свойствами класса
    static propTypes = {
        /** дополнительный класс */
        className: PropTypes.string,
        /** дочерние элементы */
        children: PropTypes.oneOfType([
            PropTypes.string,
            PropTypes.number,
            PropTypes.node
        ]),
        /** флаг получения данных */
        isPending: PropTypes.bool, // Название флагов начинаются с "is"
        /** Заголовок блока и попапа */
        title: PropTypes.string,
        /** текст описания */
        description: PropTypes.string,
        /** обработчик нажатия на "показать описание" */
        onShowDescription: PropTypes.func, // названия обработчиков начинаются с "on"
    };

    static defaultProps = {
        isPending: false,
        title: '',
        description: '',
        onShowDescription: () => {},
    };
    // 2 строки перед методами
    // ---//---
    constructor(props) {
        super(props);

        this.state = {
            isShowInfo: false
        }
    }


    /**
     * Обработчик нажатия на "показать описание"
     */
    handleShowInfo = () => {  // Название метода обработчика начинается с "handle"
        this.setState(
            { isShowInfo: true }, 
            () => this.props.onShowDescription(),
        ); 
    };


    /**
     * Обработчик нажатия на "скрыть описание"
     */
    handleCloseInfo = () => {
        this.setState({ isShowInfo: false });
    };


    /**
     * Рендер описания
     */
    renderDescription() {
        const { description } = this.props;
        
        return description 
            ? (
                <span onClick={this.handleShowInfo}>
                    <InfoIcon className={`${ActionRow.displayName}__icon`} />
                </span>
            ) 
            : null;
    }
    
    
    /**
     * Рендер информационного попапа
     */
    renderInfoPopup() { // Методы рендера начинаются с "render"
        const { 
            title, 
            description,
        } = this.props;
        const { isShowInfo } = this.state;

        return description && isShowInfo ? (
            <PopupInfo
                open
                title={title}
                message={description}
                onClose={this.handleCloseInfo}
            />
        ) : null;
    }


    render() {
        const { 
            className, 
            title, 
            children, 
            isPending,
        } = this.props;

        const blockClasses = cx(ActionRow.displayName, {
            [`${ActionRow.displayName}_is-pending`]: isPending,
        }, className);
        
        return (
            <div className={blockClasses}>
                <Heading
                    className={`${ActionRow.displayName}__title`}
                    level="3" // Более одного атрибута переносятся
                >
                    {title}
                    {this.renderDescription()}
                </Heading>

                <div className={`${ActionRow.displayName}__content`}>
                    {children}
                </div>

                {this.renderInfoPopup()}
            </div>
        );
    }
}
// 2 строки перед экспортом
// ---//---
export default ActionRow;
// Пустая строка в конце файла
```

#### Пример компонента-функции
```javascript
import React from 'react';
import PropTypes from 'prop-types';
import cx from 'classnames';
import QRC from 'qrcode.react';

import './QRCode.styl';


/**
 * Компонент вывода QR кода по значению
 */
const QRCode = (props) => {
    const { 
        className, 
        value, 
        size,
        renderAs,
    } = props;
    
    return (
        <div className={cx(QRCode.displayName, className)}>
            <QRC
                value={value}
                size={size}
                renderAs={renderAs}
            />
        </div>
    );
};

QRCode.propTypes = {
    /** Доп. классы */
    className: PropTypes.string,
    /** Значение QR кода */
    value: PropTypes.string.isRequired,
    /** Длина сторны квадрата QR кода */
    size: PropTypes.number,
	/** Тег для рендера */
    renderAs: PropTypes.string,
};

QRCode.defaultProps = {
    size: 128,
    renderAs: 'svg',
};

QRCode.displayName = 'QRCode';


export default QRCode;
```

### Стили
Порядок свйоств согласно [Академии HTML](http://codeguide.academy/html-css.html#css-order)

Отступ: 2

#### Пример стилей (stylus)

```stylus
.ActionRow
  display flex
  align-items center
  min-height 64px
  padding 1rem 4rem

  background-color white

  &:not(:first-child)
    margin-top 1px

  &__title
    display flex
    align-items center
    align-content center
    width 40%

  &__content
    width 60%

  &__icon
    width 20px
    height 20px
    margin-left 1rem

    color white
    background-color $filterColor
    border-radius 50%
```
