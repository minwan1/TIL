## Spring


@Consumes : 수신 하고자하는 데이터 포맷을 정의한다.(request)
@Produces : 출력하고자 하는 데이터 포맷을 정의한다.(response)

Produces와 Consumes 차이 : @Produces는 Accept 헤더를 참고하고, @Consumes은 Content-Type 헤더를 참고함

@RequestMapping
컨트롤러에 기본이 되는 요청정보를 설정하는 어노테이션이다.
Accept 와 content-Type 에 대한 접근 허용을 사용하기 위해 header 라는 속성을 사용했다면, 3.1부터는 consumes 와 produces 로 나눠 사용할 수 있다.


## spring 의 컨트롤러에서 gson의 JsonObject를 리턴하면 에러나는이유.
Spring 에서 @responsebody을 사용하게되면 RequestResponseBodyMethodProcessor 는 MappingJackson2HttpMessageConverter 이핸들러를 사용하여 serialize한다. 내부적으로 MappingJackson2HttpMessageConverter는 ObjectMapper을 사용한다. 기본적으로 ObjectMapper는 클래스의 getter을 이용하여 serialize하여 json형식으로 만든다.
그래서 gson은 출력안되고 JSONObject는 json형식으로 출력해주는듯.
참고

## javascript

이렇게 contentType을 application/json으로 명시해줄 경우 반드시 Stringify를 해야 한다. 그렇지 않으면, 기본적으로 Ajax는 data값이 string이 아닐 경우 pre-process과정을 통해 data obejct를 query String형태(?key=value&key=value&…)로 변경시킨다. 결국 서버에서는 JSON형태가 아니기에 잘못된 파라미터로 인식하고 400 error(Bad request)를 리턴 할 것이다.

* [Syaku 샤쿠](http://syaku.tistory.com/277)
* [참고](https://stackoverflow.com/questions/19204048/returning-jsonobject-using-responsebody-in-springmvc)











package com.sodatransfer.web.transfer;

import java.sql.Timestamp;


import javax.persistence.Column;
import javax.persistence.Entity;
import javax.persistence.EnumType;
import javax.persistence.Enumerated;
import javax.persistence.FetchType;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;
import javax.persistence.JoinColumn;
import javax.persistence.OneToOne;
import javax.persistence.Table;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

@Entity
@Table(name = "transfer_data")
public class TransferData {

	private static final Logger logger = LoggerFactory.getLogger(TransferData.class);

	public static enum TransferState {
		waiting, confirming, transferring, cancelled, completed
	}

	public static enum CurrencyState{
		KRW,USD
	}

	public static enum PaymentType {
		CreditCard, local, Paygate
	}

	@Id
	@Column(name = "id")
	@GeneratedValue(strategy = GenerationType.IDENTITY)
	private long id;

	@Column(name = "src_currency")
	private String srcCurrency;

	@Column(name = "dst_currency")
	private String dstCurrency;

	@Column(name = "src_money")
	private double srcMoney;

	@Column(name = "dst_money")
	private double dstMoney;

	@Column(name = "exchange_rate")
	private double exchangeRate;

	@Column(name = "create_date")
	private long createDate;

	@Column(name = "pay_date")
	private long payDate;

	@Column(name = "prediction_date")
	private long predictionDate;

	@Column(name = "receive_date")
	private long receiveDate;

	@Enumerated(EnumType.STRING)
	@Column(name = "current_state")
	private TransferState currentState;

	@Enumerated(EnumType.STRING)
	@Column(name = "payment_type")
	private PaymentType paymentType;

	@Column(name = "sender_name")
	private String senderName;

	@Column(name = "sender_id")
	private String senderId;

	@Column(name = "recipient_first_name")
	private String recipientFirstName;

	@Column(name = "recipient_last_name")
	private String recipientLastName;

	@Column(name = "recipient_id")
	private String recipientId;

	@Column(name = "recipient_bank_code")
	private String recipientBankCode;

	@Column(name = "recipient_bank_name")
	private String recipientBankName;

	@Column(name = "routing_number")
	private String routingNumber;

	@Column(name = "recipient_account_number")
	private String recipientAccountNumber;

	@Column(name = "recipient_country")
	private String recipientCountry;

	@Column(name = "recipient_city")
	private String recipientCity;

	@Column(name = "recipient_address")
	private String recipientAddress;

	@Column(name = "recipient_state")
	private String recipientState;

	@Column(name = "recipient_zip")
	private String recipientZip;

	@Column(name = "recipient_email")
	private String recipientEmail;

	private double fee;

	@Column(name = "saved_money")
	private double savedMoney;

	@Column(name = "average_fees")
	private double averageFees;

	@Column(name = "reduced_fee")
	private double reducedFee;

	@Column(name = "local_bank_id")
	private long localBankId;

	@Column(name = "transfer_id")
	private String transferId;

	private String note;

	@Column(name = "dst_money_requested")
	private double dstMoneyRequested;

	@Column(name = "exchange_rate_requested")
	private double exchangeRateRequested;

	@Column(name = "prediction_date_requested")
	private long predictionDateRequested;

	@Column(name = "updated_date")
	private long updatedDate;

	@Column(name = "error_code")
	private int errorCode;

	@Column(name = "cancel_msg")
	private String cancelMsg;

	@Column(name = "created_at")
	private Timestamp createdAt;

	@Column(name = "updated_at")
	private Timestamp updatedAt;

	@Column(name = "created_by")
	private String createdBy;

	@Column(name = "updated_by")
	private String updatedBy;

	@OneToOne(fetch = FetchType.LAZY)
	@JoinColumn(name = "transfer_id", referencedColumnName = "soda_id")
	private TransferMapping transferMapping;

	@Column(name = "kr_tid")
	private String krTid;


	public String getKrTid() {
		return krTid;
	}

	public void setKrTid(String krTid) {
		this.krTid = krTid;
	}

	public TransferMapping getTransferMapping() {
		return transferMapping;
	}

	public void setTransferMapping(TransferMapping transferMapping) {
		this.transferMapping = transferMapping;
	}

	public double getReducedFee() {
		return reducedFee;
	}

	public void setReducedFee(double reducedFee) {
		this.reducedFee = reducedFee;
	}

	public String getRecipientBankName() {
		return recipientBankName;
	}

	public void setRecipientBankName(String recipientBankName) {
		this.recipientBankName = recipientBankName;
	}

	public String getRoutingNumber() {
		return routingNumber;
	}

	public void setRoutingNumber(String routingNumber) {
		this.routingNumber = routingNumber;
	}

	public String getRecipientEmail() {
		return recipientEmail;
	}

	public void setRecipientEmail(String recipientEmail) {
		this.recipientEmail = recipientEmail;
	}

	public String getTransferId() {
		return transferId;
	}

	public void setTransferId(String transferId) {
		this.transferId = transferId;
	}

	public double getSavedMoney() {
		return savedMoney;
	}

	public void setSavedMoney(double savedMoney) {
		this.savedMoney = savedMoney;
	}

	public double getAverageFees() {
		return averageFees;
	}

	public void setAverageFees(double averageFees) {
		this.averageFees = averageFees;
	}

	public double getFee() {
		return fee;
	}

	public void setFee(double fee) {
		this.fee = fee;
	}

	public String getSrcCurrency() {
		return srcCurrency;
	}

	public void setSrcCurrency(String srcCurrency) {
		this.srcCurrency = srcCurrency;
	}

	public String getDstCurrency() {
		return dstCurrency;
	}

	public void setDstCurrency(String dstCurrency) {
		this.dstCurrency = dstCurrency;
	}

	public double getSrcMoney() {
		return srcMoney;
	}

	public void setSrcMoney(double srcMoney) {
		this.srcMoney = srcMoney;
	}

	public double getDstMoney() {
		return dstMoney;
	}

	public void setDstMoney(double dstMoney) {
		this.dstMoney = dstMoney;
	}

	public double getExchangeRate() {
		return exchangeRate;
	}

	public void setExchangeRate(double exchangeRate) {
		this.exchangeRate = exchangeRate;
	}

	public long getCreateDate() {
		return createDate;
	}

	public void setCreateDate(long createDate) {
		this.createDate = createDate;
	}

	public long getPayDate() {
		return payDate;
	}

	public void setPayDate(long payDate) {
		this.payDate = payDate;
	}

	public long getPredictionDate() {
		return predictionDate;
	}

	public void setPredictionDate(long predictionDate) {
		this.predictionDate = predictionDate;
	}

	public long getReceiveDate() {
		return receiveDate;
	}

	public void setReceiveDate(long receiveDate) {
		this.receiveDate = receiveDate;
	}

	public TransferState getCurrentState() {
		return currentState;
	}

	public void setCurrentState(TransferState currentState) {
		this.currentState = currentState;
	}

	public PaymentType getPaymentType() {
		return paymentType;
	}

	public void setPaymentType(PaymentType paymentType) {
		this.paymentType = paymentType;
	}

	public String getSenderName() {
		return senderName;
	}

	public void setSenderName(String senderName) {
		this.senderName = senderName;
	}

	public String getSenderId() {
		return senderId;
	}

	public void setSenderId(String senderId) {
		this.senderId = senderId;
	}

	public String getRecipientFirstName() {
		return recipientFirstName;
	}

	public void setRecipientFirstName(String recipientFirstName) {
		this.recipientFirstName = recipientFirstName;
	}

	public String getRecipientLastName() {
		return recipientLastName;
	}

	public void setRecipientLastName(String recipientLastName) {
		this.recipientLastName = recipientLastName;
	}

	public String getRecipientBankCode() {
		return recipientBankCode;
	}

	public void setRecipientBankCode(String recipientBankCode) {
		this.recipientBankCode = recipientBankCode;
	}

	public String getRecipientAccountNumber() {
		return recipientAccountNumber;
	}

	public void setRecipientAccountNumber(String recipientAccountNumber) {
		this.recipientAccountNumber = recipientAccountNumber;
	}

	public String getRecipientCountry() {
		return recipientCountry;
	}

	public void setRecipientCountry(String recipientCountry) {
		this.recipientCountry = recipientCountry;
	}

	public String getRecipientCity() {
		return recipientCity;
	}

	public void setRecipientCity(String recipientCity) {
		this.recipientCity = recipientCity;
	}

	public String getRecipientAddress() {
		return recipientAddress;
	}

	public void setRecipientAddress(String recipientAddress) {
		this.recipientAddress = recipientAddress;
	}

	public String getRecipientId() {
		return recipientId;
	}

	public void setRecipientId(String recipientId) {
		this.recipientId = recipientId;
	}

	public int getErrorCode() {
		return errorCode;
	}

	public void setErrorCode(int errorCode) {
		this.errorCode = errorCode;
	}

	public long getId() {
		return id;
	}

	public void setId(long id) {
		this.id = id;
	}

	public String getNote() {
		return note;
	}

	public void setNote(String note) {
		this.note = note;
	}

	public TransferData() {
		super();
	}

	public long getLocalBankId() {
		return localBankId;
	}

	public void setLocalBankId(long localBankId) {
		this.localBankId = localBankId;
	}

	public String getRecipientZip() {
		return recipientZip;
	}

	public void setRecipientZip(String recipientZip) {
		this.recipientZip = recipientZip;
	}

	public String getRecipientState() {
		return recipientState;
	}

	public void setRecipientState(String recipientState) {
		this.recipientState = recipientState;
	}

	public double getDstMoneyRequested() {
		return dstMoneyRequested;
	}

	public void setDstMoneyRequested(double dstMoneyRequested) {
		this.dstMoneyRequested = dstMoneyRequested;
	}

	public double getExchangeRateRequested() {
		return exchangeRateRequested;
	}

	public void setExchangeRateRequested(double exchangeRateRequested) {
		this.exchangeRateRequested = exchangeRateRequested;
	}

	public long getPredictionDateRequested() {
		return predictionDateRequested;
	}

	public void setPredictionDateRequested(long predictionDateRequested) {
		this.predictionDateRequested = predictionDateRequested;
	}

	public long getUpdatedDate() {
		return updatedDate;
	}

	public void setUpdatedDate(long updatedDate) {
		this.updatedDate = updatedDate;
	}

	public TransferData(long id, String srcCurrency, String dstCurrency, double srcMoney, double dstMoney,
			double exchangeRate, long createDate, long payDate, long predictionDate, long receiveDate,
			TransferState currentState, PaymentType paymentType, String senderName, String senderId,
			String recipientFirstName, String recipientLastName, String recipientBankCode, String recipientBankName,
			String recipientAccountNumber, String routingNumber, String recipientCountry, String recipientCity,
			String recipientAddress, String recipientState, String recipientZip, String recipientEmail, String recipientId,
			int errorCode) {
		super();
		this.id = id;
		this.srcCurrency = srcCurrency;
		this.dstCurrency = dstCurrency;
		this.srcMoney = srcMoney;
		this.dstMoney = dstMoney;
		this.exchangeRate = exchangeRate;
		this.createDate = createDate;
		this.payDate = payDate;
		this.predictionDate = predictionDate;
		this.receiveDate = receiveDate;
		this.currentState = currentState;
		this.paymentType = paymentType;
		this.senderName = senderName;
		this.senderId = senderId;
		this.recipientFirstName = recipientFirstName;
		this.recipientLastName = recipientLastName;
		this.recipientEmail = recipientEmail;
		this.recipientBankCode = recipientBankCode;
		this.recipientBankName = recipientBankName;
		this.recipientAccountNumber = recipientAccountNumber;
		this.routingNumber = routingNumber;
		this.recipientCountry = recipientCountry;
		this.recipientCity = recipientCity;
		this.recipientAddress = recipientAddress;
		this.recipientState = recipientState;
		this.recipientZip = recipientZip;
		this.recipientId = recipientId;
		this.errorCode = errorCode;
	}

	public TransferData(String srcCurrency, String dstCurrency, double srcMoney, double dstMoney, double exchangeRate,
			long createDate, String senderName, String senderId, String recipientFirstName, String recipientLastName,
			String recipientEmail, String recipientBankCode, String recipientBankName, String recipientAccountNumber,
			String routingNumber, String recipientCountry, String recipientCity, String recipientAddress,
			String recipientState, String recipientZip, String recipientId, long localBankId,String krTid) {
		super();
		this.srcCurrency = srcCurrency;
		this.dstCurrency = dstCurrency;
		this.srcMoney = srcMoney;
		this.dstMoney = dstMoney;
		this.exchangeRate = exchangeRate;
		this.createDate = createDate;
		this.senderName = senderName;
		this.senderId = senderId;
		this.recipientFirstName = recipientFirstName;
		this.recipientLastName = recipientLastName;
		this.recipientEmail = recipientEmail;
		this.recipientBankCode = recipientBankCode;
		this.recipientBankName = recipientBankName;
		this.recipientAccountNumber = recipientAccountNumber;
		this.routingNumber = routingNumber;
		this.recipientCountry = recipientCountry;
		this.recipientCity = recipientCity;
		this.recipientAddress = recipientAddress;
		this.recipientState = recipientState;
		this.recipientZip = recipientZip;
		this.recipientId = recipientId;
		this.localBankId = localBankId;
	}

	public void dump() {
		logger.debug("==============================================");
		logger.debug("" + id);
		logger.debug("" + srcCurrency);
		logger.debug("" + dstCurrency);

		logger.debug("" + srcMoney);
		logger.debug("" + dstMoney);
		logger.debug("" + exchangeRate);
		logger.debug("" + createDate);
		logger.debug("" + payDate);
		logger.debug("" + predictionDate);
		logger.debug("" + receiveDate);
		logger.debug("" + currentState);
		logger.debug("" + paymentType);
		logger.debug("" + senderName);
		logger.debug("" + senderId);
		logger.debug("" + recipientFirstName);
		logger.debug("" + recipientLastName);
		logger.debug("" + recipientBankCode);
		logger.debug("" + recipientAccountNumber);
		logger.debug("" + recipientCountry);
		logger.debug("" + recipientCity);
		logger.debug("" + recipientAddress);
		logger.debug("" + recipientId);
		logger.debug("" + errorCode);

		logger.debug("" + recipientEmail);
		logger.debug("" + recipientBankCode);
		logger.debug("" + recipientBankName);
		logger.debug("" + routingNumber);
		logger.debug("==============================================");
	}

	public TransferData(long id, String srcCurrency, String dstCurrency, double srcMoney, double dstMoney,
			double exchangeRate, long createDate, long payDate, long predictionDate, long receiveDate,
			TransferState currentState, PaymentType paymentType, String senderName, String senderId,
			String recipientFirstName, String recipientLastName, String recipientBankCode, String recipientAccountNumber,
			String recipientCountry, String recipientCity, String recipientAddress, String recipientState,
			String recipientBankName, String recipientId, int errorCode, String note, double fee, double savedMoney,
			double averageFees, long localBankId, String transferId, String routingNumber, String recipientEmail,
			double reducedFee) {
		super();
		this.id = id;
		this.srcCurrency = srcCurrency;
		this.dstCurrency = dstCurrency;
		this.srcMoney = srcMoney;
		this.dstMoney = dstMoney;
		this.exchangeRate = exchangeRate;
		this.createDate = createDate;
		this.payDate = payDate;
		this.predictionDate = predictionDate;
		this.receiveDate = receiveDate;
		this.currentState = currentState;
		this.paymentType = paymentType;
		this.senderName = senderName;
		this.senderId = senderId;
		this.recipientFirstName = recipientFirstName;
		this.recipientLastName = recipientLastName;
		this.recipientBankCode = recipientBankCode;
		this.recipientAccountNumber = recipientAccountNumber;
		this.recipientCountry = recipientCountry;
		this.recipientCity = recipientCity;
		this.recipientAddress = recipientAddress;
		this.recipientState = recipientState;
		this.recipientBankName = recipientBankName;
		this.recipientId = recipientId;
		this.errorCode = errorCode;
		this.note = note;
		this.fee = fee;
		this.savedMoney = savedMoney;
		this.averageFees = averageFees;
		this.localBankId = localBankId;
		this.transferId = transferId;
		this.routingNumber = routingNumber;
		this.recipientEmail = recipientEmail;
		this.reducedFee = reducedFee;
	}

	public Timestamp getCreatedAt() {
		return createdAt;
	}

	public void setCreatedAt(Timestamp createdAt) {
		this.createdAt = createdAt;
	}

	public Timestamp getUpdatedAt() {
		return updatedAt;
	}

	public void setUpdatedAt(Timestamp updatedAt) {
		this.updatedAt = updatedAt;
	}

	public String getCreatedBy() {
		return createdBy;
	}

	public void setCreatedBy(String createdBy) {
		this.createdBy = createdBy;
	}

	public String getUpdatedBy() {
		return updatedBy;
	}

	public void setUpdatedBy(String updatedBy) {
		this.updatedBy = updatedBy;
	}

}


//					TransferMapping transferMapping = data.getTransferMapping();
//					if(transferMapping != null){
//						newPayGateService.cancelSeyfertTransaction(transferMapping.getKrId());
//					}
