import React, { useState } from "react";
import { loadStripe } from "@stripe/stripe-js";
import {
  Elements,
  CardElement,
  useStripe,
  useElements,
} from "@stripe/react-stripe-js";

const stripePromise = loadStripe("pk_test_YourPublicStripeKeyHere");

function BookingForm() {
  const stripe = useStripe();
  const elements = useElements();
  const [status, setStatus] = useState(null);
  const [formData, setFormData] = useState({
    name: "",
    email: "",
    phone: "",
    service: "Knotless Braids",
    stylist: "Any Available",
    date: "",
    time: "",
  });

  const handleChange = (e) => {
    setFormData({ ...formData, [e.target.name]: e.target.value });
  };

  const handleSubmit = async (e) => {
    e.preventDefault();
    if (!stripe || !elements) return;

    const cardElement = elements.getElement(CardElement);
    const { error, paymentMethod } = await stripe.createPaymentMethod({
      type: "card",
      card: cardElement,
      billing_details: { name: formData.name, email: formData.email },
    });

    if (error) {
      setStatus(error.message);
    } else {
      setStatus("✅ Deposit paid and appointment booked successfully! Check your email.");
      // TODO: Send data to backend & email confirmation
    }
  };

  return (
    <div className="min-h-screen bg-gradient-to-br from-gray-950 via-black to-gray-900 text-white font-[Poppins] p-8">
      <div className="max-w-5xl mx-auto bg-black bg-opacity-60 rounded-3xl shadow-2xl p-10 backdrop-blur-xl animate-fade-in">
        <h2 className="text-5xl font-bold text-center mb-12 text-purple-400 tracking-wide drop-shadow-xl">
          ✨ Book Your Luxury Appointment
        </h2>
        <form onSubmit={handleSubmit} className="grid grid-cols-1 md:grid-cols-2 gap-8">
          <div className="space-y-6">
            <div>
              <label className="block mb-2 text-sm uppercase tracking-widest text-purple-300 font-semibold">Full Name</label>
              <input type="text" name="name" required value={formData.name} onChange={handleChange} className="w-full px-5 py-3 rounded-xl bg-gray-900 border border-purple-600 text-white focus:outline-none focus:ring-2 focus:ring-purple-500" />
            </div>
            <div>
              <label className="block mb-2 text-sm uppercase tracking-widest text-purple-300 font-semibold">Email Address</label>
              <input type="email" name="email" required value={formData.email} onChange={handleChange} className="w-full px-5 py-3 rounded-xl bg-gray-900 border border-purple-600 text-white focus:outline-none focus:ring-2 focus:ring-purple-500" />
            </div>
            <div>
              <label className="block mb-2 text-sm uppercase tracking-widest text-purple-300 font-semibold">Phone Number</label>
              <input type="tel" name="phone" required value={formData.phone} onChange={handleChange} className="w-full px-5 py-3 rounded-xl bg-gray-900 border border-purple-600 text-white focus:outline-none focus:ring-2 focus:ring-purple-500" />
            </div>
            <div>
              <label className="block mb-2 text-sm uppercase tracking-widest text-purple-300 font-semibold">Preferred Stylist</label>
              <select name="stylist" value={formData.stylist} onChange={handleChange} className="w-full px-5 py-3 rounded-xl bg-gray-900 border border-purple-600 text-white focus:outline-none focus:ring-2 focus:ring-purple-500">
                <option value="Any Available">Any Available</option>
                <option value="Jade">Jade</option>
                <option value="Amber">Amber</option>
                <option value="Chloe">Chloe</option>
              </select>
            </div>
          </div>

          <div className="space-y-6">
            <div>
              <label className="block mb-2 text-sm uppercase tracking-widest text-purple-300 font-semibold">Service</label>
              <select name="service" value={formData.service} onChange={handleChange} required className="w-full px-5 py-3 rounded-xl bg-gray-900 border border-purple-600 text-white focus:outline-none focus:ring-2 focus:ring-purple-500">
                <option value="Knotless Braids">Knotless Braids – $150</option>
                <option value="Goddess Locs">Goddess Locs – $180</option>
                <option value="Twist Styles">Twist Styles – $120</option>
              </select>
            </div>
            <div>
              <label className="block mb-2 text-sm uppercase tracking-widest text-purple-300 font-semibold">Appointment Date</label>
              <input type="date" name="date" value={formData.date} onChange={handleChange} required className="w-full px-5 py-3 rounded-xl bg-gray-900 border border-purple-600 text-white focus:outline-none focus:ring-2 focus:ring-purple-500" />
            </div>
            <div>
              <label className="block mb-2 text-sm uppercase tracking-widest text-purple-300 font-semibold">Preferred Time</label>
              <input type="time" name="time" value={formData.time} onChange={handleChange} required className="w-full px-5 py-3 rounded-xl bg-gray-900 border border-purple-600 text-white focus:outline-none focus:ring-2 focus:ring-purple-500" />
            </div>
            <div>
              <label className="block mb-2 text-sm uppercase tracking-widest text-purple-300 font-semibold">Card Details (Deposit: $30)</label>
              <div className="p-5 bg-gray-900 border border-purple-600 rounded-xl">
                <CardElement options={{ style: { base: { color: "#fff" } } }} />
              </div>
              {status && <p className="mt-3 text-purple-400 text-sm italic animate-pulse">{status}</p>}
            </div>
          </div>
          <div className="col-span-1 md:col-span-2">
            <button type="submit" className="w-full py-5 text-xl font-bold bg-gradient-to-r from-purple-700 via-fuchsia-700 to-pink-700 hover:scale-105 transition-transform duration-300 rounded-xl shadow-xl uppercase tracking-widest">
              Confirm Booking & Pay Deposit
            </button>
          </div>
        </form>
      </div>
    </div>
  );
}

export default function BookingPage() {
  return (
    <Elements stripe={stripePromise}>
      <BookingForm />
    </Elements>
  );
}
